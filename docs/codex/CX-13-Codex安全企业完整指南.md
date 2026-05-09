# CX-13 安全与企业指南：围绕 App 建立权限、审计和合规边界

> 本篇是 App 主线的安全与企业篇。
>
> 主要来源：OpenAI Codex Settings、Config、Rules、Hooks、MCP、Plugins、Automations 官方文档。

## 本课目标

你会学会：

- App 使用中的主要风险点。
- 审批、沙盒、Rules、Hooks 各自负责什么。
- MCP、Plugins、Connectors、Automations 的安全边界。
- 企业团队如何设置默认基线。

## 1. 安全模型总览

Codex App 安全不是“完全不让它做事”，而是让每类动作有边界：

| 风险 | 控制手段 |
|---|---|
| 读敏感文件 | AGENTS.md、权限、沙盒、Rules |
| 运行危险命令 | 审批、Rules、Hooks |
| 大量改文件 | Worktree、Review、Git |
| 外部数据访问 | Connectors 最小授权 |
| 工具调用风险 | MCP 权限和审计 |
| 后台任务失控 | Automation 只读优先 |

## 2. 审批与沙盒

推荐基线：

| 场景 | 审批 | 沙盒 |
|---|---|---|
| 只读审计 | 不允许写入 | read-only |
| 日常开发 | 危险命令审批 | workspace-write |
| 陌生仓库 | 保守审批 | read-only 起步 |
| CI | 外部隔离 + 最小权限 | 看 runner 环境 |
| 生产数据 | 默认拒绝 | 明确人工授权 |

App 落地时按这个顺序做：

1. App Settings 里使用保守审批。
2. 陌生项目先只读分析。
3. 写入前看文件范围。
4. 写完用 App Review 看 diff。
5. 大任务用分支或 worktree。
6. 自动化、插件、MCP 先只读试跑。

## 3. Rules

Rules 适合硬性限制命令：

```toml
[[rules]]
match = "rm -rf *"
action = "deny"
message = "禁止危险递归删除。"
```

团队应该用 Rules 拦住：

- 递归删除。
- 生产数据库写操作。
- 直接推送 main。
- 输出 secret 的命令。
- 未经确认的部署命令。

## 4. Hooks

Hooks 是事件脚本。适合：

- 审计日志。
- 命令前检查。
- 文件写入后自动格式化。
- 提交前策略检查。

风险：

- Hook 本身会运行代码。
- Hook 可以泄露上下文。
- Hook 写错会阻塞开发。

企业环境要对 Hook 做代码审查。

## 5. MCP 安全

MCP 工具可能访问外部系统。检查清单：

- 是否只读？
- 是否访问生产？
- 是否会联网？
- 是否有审计日志？
- 是否需要用户级授权？
- 是否能限定路径 / 数据库 / 表 / 仓库？

默认不要给生产写权限。

## 6. Plugins / Connectors 安全

安装和授权分开看：

| 步骤 | 要问的问题 |
|---|---|
| 安装 Plugin | 它带了什么能力？ |
| 启用 Connector | 它能访问哪些账号数据？ |
| 授权 scope | 是否过大？ |
| 使用外部上下文 | 是否包含敏感信息？ |
| 卸载 | 是否撤销外部授权？ |

## 7. Automations 安全

Automation 默认建议只读：

```text
每天检查测试失败并汇总，不要修改文件。
```

允许写入时必须限制：

- 目录范围。
- 文件类型。
- 分支或 worktree。
- 是否提交。
- 是否通知人工。

## 8. 企业基线

建议最小基线：

```text
AGENTS.md
.codex/config.toml
.rules
必要的 hooks
插件和 MCP 白名单
连接器授权说明
自动化任务登记表
```

登记表至少包含：

| 类别 | 必填 |
|---|---|
| Plugin / Connector | 名称、来源、授权 scope、owner、撤销方式 |
| MCP | server 名、访问数据、读写权限、token 存放方式 |
| Automation | 运行频率、目录、权限、输出位置、停止条件 |
| Rules / Hooks | 规则目的、触发条件、维护人、回滚方式 |
| 高风险命令 | 是否允许、谁审批、日志位置 |

团队要明确：

- 谁能改配置。
- 谁能安装插件。
- 哪些 MCP 允许用。
- 哪些目录禁止写。
- 自动化任务谁负责。

## 9. 敏感文件

在 `AGENTS.md` 中写：

```markdown
## Safety
- 不读取或修改 `.env*`。
- 不输出 token、cookie、API key。
- 不访问生产数据库，除非任务明确要求且用户确认。
- 不直接推送 main。
```

不要只依赖 `.gitignore`。模型仍可能读到工作区里的文件，关键规则要写清楚。

## 10. Windows 团队额外注意

如果团队主要使用 Windows：

| 项 | 建议 |
|---|---|
| 安装 | 统一说明 Microsoft Store / `winget -s msstore` |
| 防火墙 | 首次启动只允许专用网络，企业策略由管理员配置 |
| 终端 | 说明默认 PowerShell，WSL2 项目要单独配置 |
| 路径 | 教程示例避免只写 macOS/Linux 路径 |
| 执行策略 | PowerShell 脚本运行策略由团队统一约定 |

安全教程不能只按 macOS/Linux 习惯写，否则 Windows 用户会卡在安装、权限和本地通信上。

## 常见问题

### Q1：可以彻底跳过审批和沙盒吗？

只在外部已经隔离的一次性环境中考虑。日常 App 使用不建议。

### Q2：企业应该先管什么？

先管插件、连接器、MCP、自动化和危险命令。它们的外部影响最大。

### Q3：安全规则放 AGENTS.md 还是 Rules？

项目事实和说明放 AGENTS.md；硬性命令策略放 Rules；事件脚本放 Hooks。

## 下一步

下一篇：[CX-14 Codex 与 Claude Code 对比](./CX-14-Codex与Claude-Code对比指南.md)。
