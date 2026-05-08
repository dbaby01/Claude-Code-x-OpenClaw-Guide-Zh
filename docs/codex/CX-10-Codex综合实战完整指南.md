# Codex 综合实战完整指南：从零搭建一个完整项目的全流程

> **课程信息**
>
> - **作者**：老金
> - **GitHub**：https://github.com/KimYx0207
> - **公众号**：老金带你玩AI
> - **X（Twitter）**：老金带你玩AI
> - **个人博客**：https://aiking.dev
> - **预计学时**：2-3小时
> - **难度等级**：⭐⭐⭐ 进阶
> - **更新日期**：2026年5月
> - **适用版本**：Codex CLI v0.128.0（验证于 2026-04-30）/ Codex App 当前版本
> - **信息来源**：[GitHub](https://github.com/openai/codex) | [Codex 官方文档](https://openai.com/codex)
> - **前置课程**：CX-01 安装 / CX-02 App / CX-05 配置系统

---

> **定位说明**：本篇是 Codex 教程系列的收尾实战篇。它不是独立教程——你至少需要完成 CX-01 安装和 CX-02 App 基础后再看。本篇把 CX-01 到 CX-09 的知识点串联成一个完整项目流程。

## 📚 本课学习目标

完成本课后，你将能够：

1. 用 Codex App 从零搭建一个完整的 Node.js 项目
2. 编写 AGENTS.md、Skills、Rules 和 Hooks 形成项目规范
3. 接入 MCP Server 获取外部文档
4. 配置 Automations 做持续检查
5. 用 Review 面板审查和合并 Codex 的修改
6. 设计团队级的 Codex 工作流

---

## 第1部分：项目规划

### 1.1 项目选型

本篇用一个真实场景演示：一个 Express + TypeScript 的 REST API 项目。

为什么选这个：
- 足够简单，但覆盖文件创建、修改、测试、文档
- 可以演示 AGENTS.md、Skills、MCP、Automations、Review 全部环节
- 换成你自己的项目，流程一样

### 1.2 学习路径映射

本篇每一步对应哪篇教程：

| 实战步骤 | 对应教程 | 用到什么 |
|---------|---------|---------|
| 创建项目 + 初始化 | CX-02 | App Local 线程 |
| 编写 AGENTS.md | CX-05 | 项目指令 |
| 配置 config.toml | CX-05 | 审批、沙盒、rules |
| 编写 Skills | CX-06 | 可复用工作流 |
| 接入 MCP | CX-07 | 外部文档 |
| 配置 Automations | CX-08 | 后台周期检查 |
| 安全审查 | CX-09 | 沙盒、hooks、权限 |
| Review 合并 | CX-02 | Review 面板 |

---

## 第2部分：初始化项目

### 2.1 创建 Git 仓库

```bash
mkdir codex-demo-api
cd codex-demo-api
git init
```

### 2.2 在 Codex App 中打开

1. 打开 Codex App
2. 点击 "New Thread"
3. 选择 `codex-demo-api` 目录
4. 模式选 Local

### 2.3 第一个任务：初始化项目

在 App 中输入：

```text
初始化一个 Express + TypeScript 项目：
1. package.json，name 为 codex-demo-api
2. tsconfig.json，target ES2022，module NodeNext
3. src/index.ts 作为入口，启动 Express 监听 3000 端口
4. 一个健康检查端点 GET /health 返回 { status: "ok" }
5. 安装依赖并确认 npm run dev 能启动
```

Codex 会创建文件、安装依赖。完成后用 Review 面板审查改动。

---

## 第3部分：编写 AGENTS.md

### 3.1 为什么先写 AGENTS.md

AGENTS.md 是 Codex 理解项目的核心文件。写了它，后续所有 Agent 操作都会遵守你的规范。

### 3.2 实战 AGENTS.md

在项目根目录创建 `AGENTS.md`：

```markdown
# codex-demo-api

## 项目概述
Express + TypeScript REST API。

## 技术栈
- Node.js 22+
- TypeScript 5.x
- Express 4.x
- vitest（测试）

## 常用命令
- `npm run dev`：启动开发服务器
- `npm run build`：编译 TypeScript
- `npm test`：运行测试

## 代码规范
- 使用 ES modules（import/export）
- 函数和变量用 camelCase
- 类型和接口用 PascalCase
- 文件命名用 kebab-case
- 每个 API 端点一个文件，放在 src/routes/

## 测试规范
- 测试文件放在 tests/ 目录
- 文件名格式：*.test.ts
- 修改路由后运行相关测试

## 禁止事项
- 不要修改 tsconfig.json 除非明确要求
- 不要安装新的框架（如 Nest、Fastify）
- 不要在 src/ 外创建新目录
- 不要硬编码密钥或密码
```

### 3.3 验证效果

再给 Codex 一个任务，看它是否遵守规范：

```text
创建一个用户相关的 API 路由：
- GET /api/users 返回用户列表（先写 mock 数据）
- GET /api/users/:id 返回单个用户
- 按项目规范放在正确的文件中
```

观察：Codex 应该把路由放在 `src/routes/users.ts`，而不是随意位置。

---

## 第4部分：配置 Rules 和 config.toml

### 4.1 项目级配置

创建 `.codex/config.toml`：

```toml
# 项目级配置
approval_policy = "on-request"
sandbox_mode = "workspace-write"

# 使用 Node.js 项目常用的 rules
[[rules]]
name = "allow-npm"
allow = ["npm test", "npm run *", "npx *"]

[[rules]]
name = "allow-git-read"
allow = ["git log", "git diff", "git status"]
```

### 4.2 Rules 文件

创建 `.codex/rules/safety.rules`：

```text
allow npm test
allow npm run *
allow npx *
allow git log
allow git diff
allow git status
deny git push
deny rm -rf
deny sudo
```

### 4.3 验证效果

让 Codex 尝试执行禁止命令：

```text
运行 git push origin main
```

Codex 应该被 rules 拦截或请求确认。

---

## 第5部分：编写 Skills

### 5.1 审查 Skill

创建 `.codex/skills/code-review/SKILL.md`：

```markdown
---
name: code-review
description: 代码审查工作流
---

# Code Review Skill

## Workflow

1. 获取当前分支相对于 main 的所有变更文件
2. 逐文件检查：
   - 代码风格是否符合 AGENTS.md 规范
   - 是否有缺失的错误处理
   - 是否有硬编码密钥
   - 新路由是否有对应测试
3. 输出审查报告，按 CRITICAL / WARNING / INFO 分级

## Output

- Markdown 格式审查报告
- 每个问题标注文件名和行号
- 给出修复建议

## Rules

- 不修改任何文件
- 不运行测试
- 只读分析
```

### 5.2 测试 Skill

```text
运行 $code-review，审查当前所有未提交的变更
```

### 5.3 测试生成 Skill

创建 `.codex/skills/gen-test/SKILL.md`：

```markdown
---
name: gen-test
description: 为指定路由生成测试
---

# Generate Tests Skill

## Workflow

1. 读取用户指定的路由文件
2. 分析所有导出的函数和端点
3. 用 vitest 生成测试文件到 tests/ 目录
4. 覆盖：正常响应、错误响应、边界情况

## Rules

- 测试文件命名：{route-name}.test.ts
- 使用 vitest 的 describe/it/expect
- 不修改原路由文件
```

使用：

```text
运行 $gen-test 为 src/routes/users.ts 生成测试
```

---

## 第6部分：接入 MCP Server

### 6.1 添加 MCP 配置

在 `~/.codex/config.toml` 或项目 `.codex/config.toml` 中添加：

```toml
[[mcp_servers]]
name = "context7"
transport_type = "stdio"
command = "npx"
args = ["-y", "@upstash/context7-mcp"]
```

### 6.2 使用 MCP 获取文档

```text
用 Context7 查询 Express 的最新路由文档，
然后按官方推荐方式重构 src/routes/users.ts，
添加输入验证和错误处理中间件。
```

Codex 会通过 MCP 获取 Express 最新文档，按官方最佳实践重构代码。

---

## 第7部分：配置 Automations

### 7.1 创建每日测试守护

在 Codex App 的 Automations 中创建：

```text
每天工作日上午 9 点运行 npm test。
如果失败，把失败测试和错误信息放到 Triage。
如果通过，自动归档。
不要修改任何文件。
```

### 7.2 创建文档漂移检查

```text
每周五下午 5 点检查 src/routes/ 和 README.md 是否一致。
如果发现 API 端点已变但 README 没更新，生成 Triage 报告。
不要直接修改文件。
```

### 7.3 验证

检查 Automations pane，确认两个任务已创建。

---

## 第8部分：安全审查

### 8.1 Hooks：提交前检查

创建 `.codex/hooks/pre-commit`（示例思路，具体实现以当前 Codex hooks 配置为准）：

在 config.toml 中配置 hooks，让每次文件修改前检查是否包含敏感信息。

### 8.2 审查清单

用 CX-09 的安全基线审查当前项目：

| 检查项 | 状态 | 说明 |
|--------|------|------|
| approval_policy 已设置 | ✅ | on-request |
| sandbox_mode 已设置 | ✅ | workspace-write |
| Rules 已配置 | ✅ | allow/deny 清单 |
| AGENTS.md 已创建 | ✅ | 禁止硬编码密钥 |
| 敏感文件保护 | 需确认 | .env 是否在 .gitignore |
| MCP 权限最小化 | ✅ | 只读文档查询 |
| Automations 权限 | ✅ | read-only |

---

## 第9部分：Review 和合并

### 9.1 审查全部变更

在 App 的 Review 面板中：
1. 逐文件查看 Codex 的所有修改
2. 对不确定的改动添加行内评论
3. 暂存（stage）确认的改动
4. 回退（revert）有问题的改动

### 9.2 最终验证

```text
运行全量测试并报告结果
```

确认全部通过后提交。

---

## 第10部分：团队工作流模板

### 10.1 个人开发者

```text
日常：
1. App Local 线程写代码
2. Review 审查
3. 提交

周期：
- Automations 每日测试
- 每周 $code-review
```

### 10.2 小团队（2-5人）

```text
共享：
- AGENTS.md 提交到 Git，所有人遵守
- Skills 提交到 .codex/skills/，通过 Git 共享
- config.toml 项目级配置提交到 .codex/config.toml

分工：
- 每人一个 Worktree 隔离工作
- 交叉 Review
- Automations 检查主分支质量
```

### 10.3 检查清单

完成本实战后，你的项目应该有：

- [ ] `AGENTS.md` — 项目指令和规范
- [ ] `.codex/config.toml` — 项目级配置
- [ ] `.codex/rules/*.rules` — 命令 allow/deny
- [ ] `.codex/skills/*/SKILL.md` — 至少一个自定义 Skill
- [ ] MCP Server 已配置（如果需要外部文档）
- [ ] Automations 已创建（如果需要周期检查）
- [ ] Review 面板审查完毕
- [ ] 所有测试通过

---

## FAQ

### Q1：我不用 Express，能用别的项目吗？

A：当然可以。本篇的核心是流程，不是技术栈。换成 Python、Go、React 前端项目都行——AGENTS.md、Skills、MCP、Automations、Review 的使用方式完全一样。

### Q2：Skills 必须放在 .codex/skills/ 吗？

A：Skills 可以放在多个位置。项目级放在 `.codex/skills/`，个人级放在 `~/.codex/skills/`，插件自带的在插件目录中。详见 CX-06。

### Q3：Automations 能在 CLI 中配置吗？

A：当前 Automations 是 App 功能，通过 App pane 管理。CLI/CI 中的自动化用 `codex exec` 或 GitHub Actions。两者是不同系统。详见 CX-08。

### Q4：MCP 和 Plugins 有什么区别？

A：MCP 连接外部工具和资源（数据库、文档、API），Plugins 是打包好的扩展包（可以包含 Skills、Apps、MCP）。需要接具体工具用 MCP，需要打包分享用 Plugin。详见 CX-07。

### Q5：项目完成后怎么维护 Codex 配置？

A：把 `AGENTS.md`、`.codex/config.toml`、`.codex/rules/`、`.codex/skills/` 都提交到 Git。新成员克隆项目后 Codex 自动读取这些配置。个人偏好（如默认模型）放在 `~/.codex/config.toml`，不提交到 Git。

---

## 术语表

| 术语 | 通俗解释 |
|------|---------|
| 综合实战 | 把之前学的所有知识点串起来做一个完整项目 |
| AGENTS.md | 给 Codex 的项目说明书 |
| Skill | 可复用的工作流程 |
| MCP | 连接外部工具的协议 |
| Automation | 后台周期自动执行的任务 |
| Review | 审查 Codex 修改的面板 |
| Rules | 控制哪些命令可以执行 |

---

## 系列回顾

| 教程 | 主题 |
|------|------|
| CX-01 | 安装与认证 |
| CX-02 | App 桌面工作台 |
| CX-03 | CLI |
| CX-04 | Web / Cloud |
| CX-05 | 配置系统 |
| CX-06 | Skills |
| CX-07 | MCP 与 Subagents |
| CX-08 | Automations 与集成 |
| CX-09 | 沙箱安全与企业 |
| CX-10 | 综合实战 |
| CX-11 | Codex vs Claude Code |

---

## 下一步

- **CX-11**：Codex 与 Claude Code 对比实战指南
- **回到 README**：按你的需求选择学习路径
