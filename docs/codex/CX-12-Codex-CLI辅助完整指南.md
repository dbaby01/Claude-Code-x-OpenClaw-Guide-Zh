# CX-12 Codex CLI 辅助指南：App 用户什么时候需要终端

> 主要来源：OpenAI Codex CLI、CLI Slash Commands、Config、MCP、Review 官方文档。

## 本课目标

你会学会：

- CLI 在 App 主线中的位置。
- 哪些任务适合 CLI。
- 常用 CLI 命令和安全边界。
- 不把 CLI 教程写成 Codex 主线。

## 1. CLI 的定位

CLI 是 App 的辅助工具，适合：

- 排查配置。
- 管理 MCP。
- 在 CI 中无头执行。
- 终端中快速 review。
- 用脚本批处理。
- Linux 或远程服务器环境。

不适合：

- 作为普通 App 用户的第一入口。
- 替代 App Review 面板。
- 让新手先背所有参数。

## 2. 常用命令

```bash
codex --help
codex
codex app
codex exec "summarize this repo"
codex review
codex mcp list
codex plugin --help
codex features
```

App 用户学习 CLI 的顺序：

1. 先会用 `codex --help` 看本机支持项。
2. 再会用 `codex app` 从终端打开或配合 App。
3. 需要无头任务时再学 `codex exec`。
4. 需要审查时再学 `codex review`。
5. 需要排查外部工具时再学 `codex mcp` 和 plugin 命令。

不要先背参数表。CLI 版本变化快，本机 `--help` 比教程里的静态列表可靠。

## 3. 审批与沙盒

CLI 常见参数：

```bash
codex --ask-for-approval on-request --sandbox workspace-write
codex --ask-for-approval never --sandbox read-only
```

危险模式只用于外部已经隔离的一次性环境：

```bash
codex --dangerously-bypass-approvals-and-sandbox
```

不要把危险模式写成团队默认推荐。

如果你不知道该选什么，App 用户默认做法是：

| 场景 | 建议 |
|---|---|
| 只读分析 | read-only / 需要审批 |
| 小范围改 docs | workspace-write / 明确目录 |
| 跑测试 | 允许普通命令，危险命令仍审批 |
| CI runner | 只给 PR diff 或目标目录 |
| 一次性隔离环境 | 才考虑无审批无沙盒 |

## 4. `codex exec`

无头执行一次任务：

```bash
codex exec "read the repo and summarize test commands"
```

适合：

- CI。
- 脚本化只读分析。
- 生成报告。

不适合：

- 需要频繁交互的大改。

一个更清楚的无头任务示例：

```bash
codex exec "Read package.json and AGENTS.md. Report the install, test, lint, and build commands. Do not modify files."
```

## 5. `codex review`

终端审查当前改动：

```bash
codex review
```

适合：

- CI PR 审查。
- 本地提交前快速检查。
- 与 App Review 交叉验证。

## 6. CLI slash commands

终端交互模式中可以输入 `/help` 查看当前 slash commands。不要把命令表写死；以当前 CLI 为准。

App 用户重点掌握：

- `/status`
- `/permissions`
- `/plan`
- `/goal`
- `/review`
- `/mcp`
- `/plugins`
- `/compact`
- `/resume`

如果这些命令没有出现在你本机 `/help`，以本机输出为准，不要按教程硬敲。

## 7. CLI 管理 MCP / Plugins

当 App 中看不到工具或需要团队统一配置时：

```bash
codex mcp --help
codex plugin --help
```

CLI 是管理和排查路径，不是普通用户调用外部工具的唯一方式。

CLI 改完 MCP 或插件后，仍要回 App 验收：

1. App 里能否看到工具或插件。
2. 线程中能否调用。
3. 权限提示是否符合预期。
4. 是否能撤销或禁用。

## 8. CI 安全原则

| 原则 | 说明 |
|---|---|
| 最小权限 | CI token 只给需要的仓库权限 |
| 外部沙箱 | 无沙盒模式只在 runner 自身可信时使用 |
| 不输出密钥 | 日志中不得打印 secret |
| 明确范围 | 只审查 PR diff 或指定路径 |
| 人工合并 | 自动评论可以，自动合并要慎重 |

## 常见问题

### Q1：App 用户不装 CLI 可以吗？

可以，但排查和 CI 会不方便。建议安装，但不先学。

### Q2：CLI 和 App 的配置完全共享吗？

不要做绝对假设。看当前版本和官方文档。能通过 App 设置解决的，优先用 App。

### Q3：CLI 能替代 Automations 吗？

不能完全替代。CLI 适合脚本和 CI；App Automations 是 App 内的后台/周期任务体验。

## 下一步

下一篇：[CX-13 安全与企业基线](./CX-13-Codex安全企业完整指南.md)。
