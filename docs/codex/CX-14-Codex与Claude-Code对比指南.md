# CX-14 Codex 与 Claude Code 对比：从 App 主线出发做工具选择

> 主要来源：OpenAI Codex App / CLI / Web 官方文档，以及 Claude Code 官方命令、插件、MCP、Hooks、Skills 文档。模型、价格和开放范围变化快，本篇不写固定数值结论。

## 本课目标

你会学会：

- 从 Codex App 主线理解 Codex 的优势。
- 判断什么时候用 Claude Code。
- 设计双工具共存策略。
- 避免用跑分、价格、模型名做硬结论。

## 1. 一句话对比

| 工具 | 更像什么 | 适合 |
|---|---|---|
| Codex App | 桌面开发主控台 | 本地多线程、Review、Automations、连接器、Cloud handoff |
| Claude Code | 终端原生 agent 工具链 | 终端深度协作、Hooks、Skills、MCP、Agent Teams、CLI/SDK |

这不是谁替代谁的问题，而是工作流重心不同。

## 2. 什么时候优先 Codex App

优先 Codex App：

- 你希望用桌面线程管理多个任务。
- 你重视 Review 面板和可视化 diff。
- 你想把 Commands、Skills、Plugins、Connectors、Automations 放在一个 App 里。
- 你需要从本地 App 接力到 Cloud / GitHub。
- 团队成员不想先学终端。

## 3. 什么时候优先 Claude Code

优先 Claude Code：

- 团队主要在终端中工作。
- 已经有 `.claude/`、Hooks、Skills、插件生态。
- 需要深度自定义 CLI / SDK / Agent Teams。
- 需要在 SSH、服务器、tmux 等终端环境中长时间运行。

## 4. 双工具共存

推荐分工：

| 任务 | Codex App | Claude Code |
|---|---|---|
| 桌面多线程开发 | 主力 | 辅助 |
| 本地 Review | 主力 | 辅助 |
| 终端自动化 | 辅助 | 主力 |
| Hooks 深度定制 | 辅助 | 主力 |
| App Automations | 主力 | 另建计划任务 |
| Cloud / GitHub handoff | 主力 | 视插件能力 |
| 复杂 Agent Teams | 视当前 App 能力 | 主力 |

## 5. 配置共存

同一仓库可以同时存在：

```text
AGENTS.md
CLAUDE.md
.codex/
.claude/
```

规则：

- `AGENTS.md` 服务 Codex。
- `CLAUDE.md` 服务 Claude Code。
- 两边都要写相同项目事实，但不要互相假设。
- 敏感规则要同步。
- 不要让两个工具同时改同一批文件。

## 6. MCP 共享

MCP server 可以在两个工具中复用，但配置方式不同。

注意：

- 不要复制过期配置。
- 分别验证工具列表。
- 权限和 token 要按工具分别管理。
- 生产系统默认只读。

## 7. 选择标准

不要问“哪个更强”，问这些：

- 团队主要在 App 还是终端？
- 是否需要桌面 Review 和 Automations？
- 是否需要终端 hooks 和 agent teams？
- 外部连接器在哪边更完整？
- 企业权限和审计哪边更容易落地？
- 当前项目是否适合云端 handoff？

## 8. 常见错误

| 错误 | 修正 |
|---|---|
| 按模型名判断工具 | 看工作流 |
| 两个工具同时改同文件 | 分支 / worktree 隔离 |
| 配置只写一边 | AGENTS.md 和 CLAUDE.md 分别维护 |
| MCP 权限复制粘贴 | 分别验证 |
| 用固定价格和跑分下结论 | 写成会变化的信息 |

## 常见问题

### Q1：只学 Codex App 可以吗？

可以。本系列就是 Codex App 主线。CLI、Web、Claude Code 都是辅助对照。

### Q2：两个工具能一起用吗？

能，但不要让它们同时改同一工作区。用分支、worktree 或明确文件范围隔离。

### Q3：团队怎么落地？

先选一个主力工具。另一个作为补充，不要一开始双主线培训。

## 系列回顾

本系列现在的主线是：

1. App 安装与登录。
2. App 桌面工作流。
3. Commands。
4. 项目指令、权限与沙盒。
5. MCP。
6. Skills。
7. Plugins / Connectors。
8. Subagents。
9. Automations。
10. Review / GitHub / PR。
11. Web / Cloud 辅助。
12. CLI 辅助。
13. 安全与企业。
14. Claude Code 对比。

## 下一步

回到 [Codex App 桌面工作流](./CX-02-Codex-App桌面工作流完整指南.md)，从 App 实战任务开始练。
