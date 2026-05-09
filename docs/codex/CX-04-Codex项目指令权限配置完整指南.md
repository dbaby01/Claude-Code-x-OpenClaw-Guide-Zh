# CX-04 项目指令、配置、权限与沙盒：让 App 知道怎么安全工作

> 本篇是 App 主线中的项目指令、权限与安全配置篇。
>
> 主要来源：OpenAI Codex Config、Rules、Hooks、Settings、CLI 官方文档。

## 本课目标

你会学会：

- 用 `AGENTS.md` 给 App 提供项目规则。
- 用 App Settings 和 `/permissions` 管理审批与沙盒。
- 理解 `config.toml`、Rules、Hooks 什么时候才需要。
- 避免把 CLI 配置当成 App 新手主线。

## 1. App 用户先写 `AGENTS.md`

`AGENTS.md` 是项目级指令文件。App 打开项目后，会把它作为重要上下文。

它应该回答：

- 项目是什么。
- 技术栈是什么。
- 常用命令是什么。
- 代码风格是什么。
- 修改后如何验证。
- 哪些文件或动作禁止。

推荐模板：

```markdown
# Project Instructions

## Overview
这是一个 Next.js 应用，主代码在 `src/`，测试在 `tests/`。

## Commands
- 安装依赖：`npm install`
- 开发服务：`npm run dev`
- 类型检查：`npm run typecheck`
- 单元测试：`npm test`
- 格式检查：`npm run lint`

## Code Style
- 使用 TypeScript strict mode。
- 不新增 `any`。
- 复用现有组件和工具函数。

## Testing
- 修改业务逻辑时补充或更新测试。
- 提交前至少运行相关测试。

## Safety
- 不读取或修改 `.env*`。
- 不提交真实密钥。
- 不直接改数据库迁移，除非任务明确要求。
```

## 2. `AGENTS.md` 不该写什么

不要写：

- API Key、token、cookie。
- 只针对本次任务的临时要求。
- 个人机器路径。
- 不稳定的模型名称或价格。
- “写高质量代码”这种无法验证的口号。

临时要求写在 App 当前线程里；长期规则才写进 `AGENTS.md`。

## 3. 多层 `AGENTS.md`

Monorepo 可以在根目录和子目录放不同的 `AGENTS.md`：

```text
repo/
  AGENTS.md
  apps/web/AGENTS.md
  packages/api/AGENTS.md
```

理解方式：

- 根目录写全局规则。
- 子目录写局部规则。
- 越靠近当前文件的指令越能补充具体上下文。
- 不要依赖“同名标题自动覆盖”这种想象，写规则时要尽量避免冲突。

## 4. App 中的权限入口

App 用户优先通过这几处理解权限：

- App Settings。
- 当前线程的审批提示。
- 当前 App 的 `/` 命令列表中显示的权限入口。
- Review 面板中的 diff。

常见策略：

| 场景 | 建议 |
|---|---|
| 陌生项目 | 保守审批，先只读 |
| 日常小改 | 允许常见读写和安全命令 |
| 大规模重构 | 使用 worktree，严格 review |
| CI / 自动化 | 外部沙箱 + 最小权限 |
| 涉及密钥 / 生产数据 | 默认拒绝，人工确认 |

## 5. 沙盒模式怎么理解

Codex 的沙盒是为了限制命令和文件访问范围。CLI 中常见模式包括：

| 模式 | 含义 |
|---|---|
| `read-only` | 只读，适合审计和探索 |
| `workspace-write` | 可写工作区，适合日常开发 |
| `danger-full-access` | 无沙盒限制，只能在外部可信沙箱中使用 |

App 用户不需要一开始背 CLI 参数，但要理解：权限越大，越需要 Review、Git 和备份。

## 6. `config.toml` 是什么

`config.toml` 是 Codex 的配置文件。它适合团队或高级用户管理：

- 默认 profile。
- 审批和沙盒默认值。
- MCP server。
- feature flags。
- 终端和环境策略。

App 主线中，普通读者不需要先手写它。推荐顺序：

1. 先在 App Settings 里完成模型、权限、终端、插件和 MCP 的基础设置。
2. 团队需要统一策略时，再查官方 Config Reference。
3. 只复制官方当前支持的字段。
4. 改完后重启或刷新 App，再用 `/status`、Settings、MCP 列表等入口验收。

如果教程需要展示配置，优先展示“要查官方 schema”而不是写死字段：

```toml
# 只作为结构提示，实际字段以官方 Config Reference 和当前 App 为准。
# 不要把模型名、实验开关或公司密钥硬写进教程示例。
```

注意：字段会随版本变化，写教程时应链接官方 Config Reference，不要复制过时字段，也不要发明 `features.goals` 这类未核实字段。

## 7. Rules：比 prompt 更硬的命令边界

Rules 用来限制或允许命令执行。适合团队把危险命令拦住：

```toml
[[rules]]
match = "rm -rf *"
action = "deny"
message = "禁止危险递归删除；需要清理时先列出目标路径并人工确认。"
```

Rules 适合：

- 禁止危险命令。
- 要求高风险命令人工审批。
- 给团队设置统一安全边界。

Rules 不适合：

- 写代码风格。
- 写长工作流。
- 替代测试。

## 8. Hooks：事件脚本

Hooks 是事件触发脚本，适合做：

- 命令执行前检查。
- 工具执行后记录日志。
- 自动格式化。
- 外部审计。

App 用户应先理解它的风险：

- Hook 会运行真实脚本。
- Hook 可能读取敏感上下文。
- Hook 写错会阻塞工作流。

学习阶段不要急着写 Hook。先用 `AGENTS.md`、Settings、Rules。

## 9. 改完配置后怎么验收

每次改 `AGENTS.md`、Settings、Rules、Hooks 或 `config.toml` 后，都按这张表验收：

| 改动 | App 中怎么验收 |
|---|---|
| `AGENTS.md` | 新线程里让 Codex 总结项目规则，看是否读到正确内容 |
| 权限 / 沙盒 | `/status` 或 Settings 显示符合预期 |
| MCP 配置 | `/mcp` 能看到 server 和工具 |
| Rules | 试一个应被拦截的无害模拟命令，确认提示正确 |
| Hooks | 在测试仓库里触发一次，看日志和副作用 |

配置没验收就等于没配置。

## 10. App 项目基线

个人项目的最小基线：

```text
AGENTS.md
  写清项目命令、测试、禁止事项

App Settings
  使用默认或保守审批

Review
  每次改动都看 diff

Git
  小步提交，不混 unrelated changes
```

团队项目再增加：

```text
.codex/config.toml
.rules
.codex/hooks/
CI 中的 codex review 或测试任务
```

## 常见问题

### Q1：可以在 `AGENTS.md` 里指定模型吗？

不建议。模型选择属于运行配置，不是项目事实。

### Q2：Rules 和 Hooks 有什么区别？

Rules 更像命令策略；Hooks 是事件脚本。能用 Rules 解决的，不要先写 Hook。

### Q3：App 用户必须学 `config.toml` 吗？

不必须。单人使用先掌握 App Settings 和 `AGENTS.md`。

## 下一步

下一篇：[CX-05 MCP：在 App 中连接外部工具](./CX-05-Codex-MCP外部工具完整指南.md)。
