# Codex 与 Claude Code 对比实战指南：双工具协作与团队决策

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
> - **适用版本**：Codex CLI v0.129.0（核对于 2026-05-08；以 `codex --version` 和 GitHub Releases 为准）/ Claude Code v2.1.133（验证于 2026-05-08）
> - **信息来源**：[Codex GitHub](https://github.com/openai/codex) | [Codex 官方文档](https://developers.openai.com/codex/) | [Claude Code GitHub](https://github.com/anthropics/claude-code) | [Claude Code 官方文档](https://docs.anthropic.com)

---

> **定位说明**：本篇是可选附录。只学 Codex App 的用户可以不看；只有当团队同时使用 Claude Code，或需要做工具迁移/共存决策时再看。

## 重要校准：不要用固定模型、价格和跑分做硬结论

旧稿里把固定模型名、固定 API 价格、Terminal-Bench 分数、记忆/日志产品名、官方 GitHub Action 等写成确定事实。模型、价格、订阅权益和 benchmark 都会快速变化，本篇不再写固定数值。

正确对比方式：

- 产品形态和工作流是否适合你
- 权限/沙盒/Review 是否符合团队要求
- 本地 vs 云端执行环境
- 插件/MCP/Skills/Hooks 能否满足工具链
- 团队已有 ChatGPT / Claude / OpenAI API / Anthropic API 账号体系

---

## 📚 本课学习目标

完成本课后，你将能够：

1. 理解 Codex 和 Claude Code 的产品形态和核心能力差异
2. 根据团队场景选择合适的主力工具
3. 设计双工具协作工作流
4. 共享 MCP Server 和配置文件
5. 避免把模型名、价格和跑分写成硬结论

---

## 第1部分：一句话对比

| 工具 | 更像什么 | 最适合 |
|------|----------|--------|
| Codex | OpenAI 的多入口编程 Agent：App / CLI / IDE / Web / SDK | 多线程本地任务、Worktree、Review、Automations、插件和 OpenAI 生态 |
| Claude Code | Anthropic 的终端编程 Agent 和自动化工具链 | 深度代码协作、Claude 模型工作流、Hooks、Skills、MCP、Agent/SDK |

两者不是互斥关系。专业团队常见做法是：选择一个作为主力，另一个作为专项工具或交叉审查工具。

---

## 第2部分：核心能力对比

| 维度 | Codex | Claude Code |
|------|-------|-------------|
| 本地桌面 App | 有，重点是 Local/Worktree/Review/Automations | 以 CLI / IDE / Desktop 相关集成为主，具体看当前官方版本 |
| CLI | `codex`，支持 `exec`、`review`、`mcp`、`plugin` 等 | `claude`，支持交互、无头、MCP、Hooks、Skills 等 |
| 项目指令 | `AGENTS.md` | `CLAUDE.md` / 项目配置 |
| 配置文件 | `~/.codex/config.toml`、项目 `.codex/config.toml` | Claude Code settings / 项目配置 |
| 沙盒/审批 | `approval_policy`、`sandbox_mode`、rules | permissions、allowed tools、bypass/accept 等策略 |
| 自动化 | App Automations、thread automations、CLI/CI | Hooks、Channels、scheduled tasks、CLI/SDK 工作流 |
| 扩展 | Plugins、Skills、Apps、MCP | Skills、Plugins、MCP、Hooks |
| Review | App Review pane、`codex review` | `/review` 口径随版本变化；也可用插件/命令/SDK |
| SDK | 官方 Codex SDK / App Server 相关能力 | Agent SDK |

---

## 第3部分：什么时候选 Codex

优先选 Codex 的场景：

- 你已经在 ChatGPT / OpenAI 生态里
- 想用桌面 App 管理多个本地任务
- 重视 Worktree 隔离、Handoff、Review 面板
- 想用 App Automations 做后台周期检查
- 想通过 Plugins 接 Gmail、Drive、Slack、Linear、GitHub 等能力
- 想在 CLI 中用 `codex review` 做轻量代码审查

---

## 第4部分：什么时候选 Claude Code

优先选 Claude Code 的场景：

- 团队已经围绕 Claude Code 建了 Hooks / Skills / MCP 工作流
- 你更偏终端重度使用
- 需要 Claude 模型在长上下文、架构讨论、深度重构上的能力
- 已有 Anthropic 账号、企业策略或内部代理
- 想用 Claude Code 的成熟命令、插件和企业治理体系

---

## 第5部分：两个都用的实战策略

### 5.1 配置文件共存

```text
repo/
├── AGENTS.md        # Codex 项目指令
├── CLAUDE.md        # Claude Code 项目指令
├── .codex/
│   └── config.toml
└── .claude/
    └── settings.json
```

原则：

- 共享“项目事实”：测试命令、目录结构、禁区
- 分开“工具配置”：模型、权限、沙盒、hooks
- 不要让两套工具同时自动改同一个文件区域

### 5.2 双工具分工

| 工作 | 推荐 |
|------|------|
| 快速扫 diff | Codex `review` 或 App Review |
| 深度架构讨论 | Claude Code |
| 多个后台本地任务 | Codex App Worktree |
| 复杂终端自动化 | Claude Code / Codex CLI 都可，按团队熟悉度 |
| 插件连接外部系统 | Codex Plugins 或 Claude Plugins，按授权体系选 |
| 交叉审查 | 一个工具实现，另一个工具 review |

### 5.3 避免冲突

- 每次只让一个工具拥有写权限
- 另一个工具用 read-only 审查
- 大任务用不同 worktree / 分支
- 提交前统一跑项目测试
- 所有外部服务 token 最小权限

---

## 第6部分：MCP 共享

Codex 和 Claude Code 都支持 MCP，但配置格式不一定相同。不要假设一份 JSON/TOML 可以直接复制。

推荐做法：

1. 先列出共享 MCP server 清单
2. 分别用两个工具的官方配置格式接入
3. token 使用相同权限策略，但不要明文提交
4. 在 `AGENTS.md` 和 `CLAUDE.md` 中写清每个 MCP 的用途和禁区

示例清单：

| MCP | 用途 | 权限 |
|-----|------|------|
| GitHub | 读 issue / PR / diff | 只读或最小写权限 |
| PostgreSQL | 查询测试库 | 只读 |
| Docs search | 查框架文档 | 只读 |

---

## 第7部分：成本和模型选择

不要用教程里的固定价格做预算。实际成本取决于：

- 你用 ChatGPT / Claude 订阅还是 API Key
- 当前模型和服务层
- 上下文长度
- 是否命中缓存
- 是否使用图片、搜索、MCP、Computer Use 等工具
- 团队并发和自动化频率

建议：

- 先用订阅内能力验证工作流
- CI 和批量任务单独走 API Key / budget 监控
- 自动化任务先 read-only，避免高成本修复循环
- 每月复盘 token / usage 数据，而不是预设固定单价

---

## 第8部分：团队落地模板

### 8.1 单主力策略

```text
主力：Codex
Claude Code：用于深度架构 review

规则：
- Codex App/CLI 负责日常改动
- Claude Code 默认 read-only review
- MCP token 最小权限
- 所有修改走 PR
```

### 8.2 双主力策略

```text
Codex：App、Worktree、Automations、插件
Claude Code：终端深度开发、Hooks、Skills

规则：
- 每个 issue 明确 owner 工具
- 不同工具用不同 branch/worktree
- 合并前交叉 review
- 共享项目指令，但不共享权限配置
```

---

## FAQ

### Q1：哪个更强？

A：不能只按模型或跑分判断。看你的工作流：桌面多任务和自动化偏 Codex；终端深度协作和 Claude 生态偏 Claude Code。

### Q2：能不能两个同时改同一仓库？

A：可以，但最好用不同 worktree / 分支，并明确写权限 owner。不要让两个 agent 同时改同一片代码。

### Q3：MCP 配置要维护两份吗？

A：通常需要按各自格式分别维护。可以用脚本从一份内部清单生成，但生成结果仍要符合各工具官方配置。

### Q4：价格怎么比？

A：看官方 Pricing 和你账号计划。教程不固定写价格，避免过期误导。

### Q5：SDK 怎么选？

A：如果你要嵌入 OpenAI Codex 工作流，看 Codex SDK；如果你要构建 Anthropic/Claude Agent 工作流，看 Claude Agent SDK。不要把 CLI 包当 SDK 用。

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

建议回到 README 的学习路径，按你的主要工具选择：

- Codex 主线：CX-01 → CX-02 → CX-03 → CX-05 → CX-08 → CX-09
- Claude Code 主线：01 → 02 → 04 → 05 → 07 → 08
- 双工具协作：先各自跑通最小工作流，再接 MCP 和插件
