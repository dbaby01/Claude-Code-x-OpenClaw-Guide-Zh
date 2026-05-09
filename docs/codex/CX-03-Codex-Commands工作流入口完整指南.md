# CX-03 Commands 完整指南：App 里的 slash commands 与工作流入口

> 本篇以 Codex App 的 slash commands 为主线。CLI slash commands 只作为核对和排查补充。
>
> 主要来源：OpenAI Codex App Commands、Codex CLI Slash Commands、Codex Follow Goals 官方文档。本篇按 2026-05-09 可查官方文档修订；命令会随 App 版本、实验开关、插件和权限变化，最终以你当前 App 输入 `/` 后看到的列表为准。

## 本课目标

你会学会：

- 在 Codex App 里把 `/` 命令当成工作流入口。
- 区分普通聊天、Commands、Skills、Automations。
- 正确使用 `/status`、`/plan-mode`、`/review`、`/mcp` 等官方已明确的 App 命令。
- 知道 `/goal`、`/loop` 这类你可能在当前环境看到的入口，应该按实验/版本相关能力处理，先确认再使用。
- 知道 CLI slash commands 哪些只是辅助核对。

## 1. Commands 在 App 里解决什么问题

Commands 不是“高级玩具”，而是 App 的快速入口：

| 你想做什么 | 直接说也可以 | Command 更适合 |
|---|---|---|
| 查看当前状态 | “现在是什么模型和权限？” | `/status` |
| 压缩上下文 | “帮我压缩上下文” | `/compact` |
| 进入计划模式 | “先给计划别执行” | `/plan-mode` |
| 长目标持续推进 | “一直做直到验证通过” | 先输入 `/` 确认是否有 `/goal` 或同类实验入口 |
| 审查改动 | “review 当前 diff” | `/review` |
| 管理外部工具 | “看看 MCP” | `/mcp` |
| 管理插件 | “打开插件列表” | `/plugins` |

学习方式：在 App 输入框输入 `/`，看当前版本展示什么。不要背死命令表，也不要把别人机器上出现的命令直接当成你这台机器一定有。

## 2. 先学会在 App 里发现命令

每次换版本、换账号、换项目或安装插件后，先做这四步：

1. 打开 Codex App 的任意项目线程。
2. 在输入框输入 `/`。
3. 看弹出的命令列表、命令说明和是否有实验标记。
4. 如果不确定某个命令能做什么，先输入 `/help` 或用自然语言问“解释当前可用 slash commands，不要执行任何改动”。

判断一个命令能不能写进教程，按这个顺序：

| 来源 | 教程写法 |
|---|---|
| 官方 App Commands 明确列出 | 可以作为稳定入口写 |
| 当前 App 弹窗显示但官方页未列出 | 写成“当前版本可能出现，以你的 App 为准” |
| 只在 CLI 出现 | 放到 CX-12，不能写成 App 主线 |
| 只听别人说过 | 不写成确定命令 |

## 3. App 用户优先掌握的稳定命令

| 命令 | App 主线用途 | 使用建议 |
|---|---|---|
| `/feedback` | 发送产品反馈 | 反馈 App 或命令问题 |
| `/status` | 查看模型、工作区、审批、上下文状态 | 任务前后都可用 |
| `/plan-mode` | 切换计划模式 | 大任务先规划，不急着改 |
| `/review` | 审查当前改动 | 合并前使用 |
| `/mcp` | 查看 MCP 工具 | 接外部工具后检查 |
| `/help` 或 `/` 列表 | 查看当前可用命令 | 以当前 App 显示为准 |

官方 App Commands 页面当前明确列出的命令很少，所以本篇不再把 CLI 的完整 slash command 表硬搬成 App 表。

## 4. `/plan-mode`：先规划，不急着写

适合：

- 多文件改动。
- 架构调整。
- 不确定影响面的 bug。
- 需要先比较方案。

示例：

```text
/plan-mode

修复登录页移动端布局问题。先读相关文件，列出可能方案、风险和验证命令，不要改文件。
```

好的计划应该包括：

- 要读哪些文件。
- 要改哪些文件。
- 可能方案和取舍。
- 验证命令。
- 不做什么。
- 什么时候退出计划模式进入执行。

如果你的 App 里显示的是 `/plan` 而不是 `/plan-mode`，按当前 App 弹窗为准；但教程中不要把两者写成永远等价。

## 5. `/goal`、`/loop`：长目标或循环入口

有些版本或实验环境可能会出现 `/goal`、`/loop` 或同类“持续推进”入口。它们适合“比一次普通提示更长、但有明确结束条件”的任务。

写法要保守：先确认你的 App 里真的有这个命令，再使用。不要在教程里保证所有用户都有。

适合：

- 长迁移。
- 长重构。
- 部署重试直到成功。
- 原型持续打磨直到测试通过。

不要这样写：

```text
/goal 优化项目
```

应该这样写：

```text
/goal Complete the migration from legacy auth helpers to the new auth client without stopping until npm test -- auth passes and no files outside src/auth and tests/auth are changed.
```

如果是 `/loop`，也要写清楚循环条件和停止条件：

```text
/loop 每 10 分钟检查一次当前分支测试状态。只汇总失败原因，不修改文件；连续两次通过或发现需要人工决策时停止。
```

长目标/循环类入口必须包含：

- 任务范围。
- 允许读写的文件。
- 验证命令。
- 失败时怎么停。
- 最长运行边界。
- 是否允许提交、推送或调用外部服务。

如果当前环境看不到 `/goal` 或 `/loop`，不要硬找配置；先用普通自然语言让 Codex 分阶段执行，或查看当前 App 的实验功能入口。

## 6. `/review`：把审查放进 App 工作流

`/review` 用来审查当前工作区改动。App 用户可以把它放在每次合并前：

```text
/review
```

你也可以自然语言补充审查重点：

```text
/review 重点看：是否改了范围外文件、是否缺测试、是否引入安全问题。
```

注意：

- `/review` 不替代人工确认。
- 高风险改动仍要读 diff。
- 安全专项可以另用安全审查提示或专门 skill。

## 7. 权限命令与安全边界

权限入口的名称可能随 App 版本变化。你应该通过 `/` 列表或 Settings 找到当前会话的权限设置，用来理解或调整 Codex 能自动做什么。

学习阶段建议：

- 不要开无限制自动执行。
- 让 Codex 在写文件和运行危险命令前解释。
- 对陌生项目使用更保守权限。

当任务涉及删除、移动、大量写入、联网或外部服务时，先看权限。若当前 App 没有 `/permissions`，就去 Settings 或当前线程权限提示里看，不要假设命令一定存在。

## 8. `/mcp`、Apps、Plugins

这三个命令常被混淆：

| 命令 | 管什么 | 例子 |
|---|---|---|
| `/mcp` | 外部工具 server 暴露的工具 | 浏览器、数据库、内部 API |
| Apps / Connectors 入口 | 连接器 / 外部服务上下文 | GitHub、Drive、Gmail、Slack |
| Plugins 入口 | 能力包 | 携带 skills、MCP、apps、配置 |

使用顺序：

1. 先确认要解决的问题。
2. 看 App 是否已有连接器或插件。
3. 用 `/mcp`、Apps 入口或 Plugins 入口确认工具是否可见。
4. 再让 Codex 使用它。

## 9. CLI slash commands：只作为补充

在终端里运行 `codex` 也有 slash commands。CLI 表只能帮助你理解概念，不能反推 App 一定支持同名命令。终端里输入 `/help` 查看当前 CLI 支持项。

| 命令 | 用途 |
|---|---|
| 类型 | 例子 | App 用户怎么用 |
|---|---|---|
| 状态 / 配置 | `/status`、`/model`、`/permissions` | 理解概念，App 里按 Settings 和 `/` 列表找对应入口 |
| 工作流 | `/review`、计划模式、目标模式 | App 优先用 Review 面板和当前命令 |
| 工具 | `/mcp`、Apps、Plugins | App 优先看工具/连接器入口 |
| 会话管理 | `/compact`、`/resume`、`/new` 等 | CLI 辅助，不写成 App 必备 |

如果你需要完整 CLI 命令表，去 CX-12 或直接运行 `codex` 后输入 `/help`。本篇只保留 App 主线。

## 10. Commands、Skills、Automations 的边界

| 类型 | 适合什么 | 例子 |
|---|---|---|
| Command | 当前会话里的快速动作 | `/review`、`/plan-mode`、`/mcp` |
| Skill | 可复用流程或领域规范 | 代码审查 SOP、发布检查 |
| Automation | 定时或后台重复任务 | 每天检查测试、每周依赖摘要 |

不要把所有东西都做成 command。长期流程优先沉淀为 Skill；周期任务用 Automation。

## 常见问题

### Q1：App 和 CLI 的命令完全一样吗？

不保证。以当前 App 的 `/` 列表、命令说明和官方 App Commands 文档为准。CLI 表只能辅助理解。

### Q2：`/goal` 或 `/loop` 会不会无限跑？

有风险。所以必须写清楚停止条件、范围和验证方式。

### Q3：我应该背完整命令表吗？

不用。掌握“输入 `/` 看当前命令”、`/status`、`/plan-mode`、`/review`、`/mcp`，再理解 Apps / Plugins / 权限入口，就足够开始。`/goal`、`/loop` 只在你的 App 确认可用时再学。

## 下一步

下一篇：[CX-04 项目指令、配置、权限与沙盒](./CX-04-Codex项目指令权限配置完整指南.md)。
