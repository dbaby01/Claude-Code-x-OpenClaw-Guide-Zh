# CX-06 Skills 完整指南：在 App 中调用和编写可复用工作流

> 主要来源：OpenAI Codex Skills 官方文档、Codex App Features、Plugins 文档。

## 本课目标

你会学会：

- Skill 在 Codex App 中是什么。
- 什么时候把一段提示词沉淀成 Skill。
- 如何调用、编写、共享 Skill。
- Skill 和 Command、Plugin、MCP、Automation 的边界。

## 1. Skill 是什么

Skill 是一组可复用指令、资源和可选脚本。它不是单纯“快捷命令”，而是一套工作流。

适合：

- 代码审查流程。
- 文档核对流程。
- 发布检查。
- 写作规范。
- 团队固定 SOP。

不适合：

- 只执行一次的临时任务。
- 需要实时外部 API 的工具连接。
- 需要定时运行的周期任务。

## 2. App 中怎么调用 Skill

常见方式：

```text
$code-review 审查当前 diff，重点看安全、测试和边界情况。
```

或自然语言触发：

```text
按我们的代码审查 Skill 检查这次改动。
```

App 也可能通过插件提供 Skills。可用 Skills 取决于当前 App、插件、项目和用户目录。

调用前先确认三件事：

1. 当前 App 是否能看到这个 Skill。
2. 这个 Skill 是用户级、项目级，还是插件带来的。
3. 它会不会读取额外资源、运行脚本或调用工具。

如果点名 `$skill-name` 没有触发，先检查名称、路径、插件是否安装，以及 App 当前会话是否已加载。

## 3. Skill 的文件结构

最小结构：

```text
.codex/skills/code-review/
  SKILL.md
```

放在哪里要分清：

| 位置 | 适合 |
|---|---|
| 项目级 `.codex/skills/` | 当前仓库专用 SOP |
| 用户级 skills 目录 | 个人跨项目复用 |
| 插件内 Skill | 团队或市场分发的能力包 |

项目级 Skill 更适合教程，因为读者打开仓库就能看到规则；用户级 Skill 适合个人工作流，不应该写进团队仓库。

示例：

```markdown
---
name: code-review
description: Review code changes for bugs, regressions, security risks, and missing tests.
---

# Code Review

## Workflow
1. Inspect the current diff.
2. Identify behavior changes.
3. Look for bugs, security risks, and missing tests.
4. Report findings first, ordered by severity.

## Output
- Findings with file and line references.
- Open questions.
- Test gaps.

## Rules
- Do not rewrite code unless explicitly asked.
- Do not praise changes before listing findings.
```

## 4. 好 Skill 的标准

| 标准 | 说明 |
|---|---|
| 目标明确 | 一句话说清它解决什么问题 |
| 触发清晰 | 用户点名或描述能稳定触发 |
| 步骤可执行 | 不写空泛原则 |
| 输出稳定 | 有明确交付格式 |
| 资源最小 | 只带必要参考文件和脚本 |
| 安全边界明确 | 写清哪些动作不能做 |

## 5. 什么时候从 prompt 升级成 Skill

满足任一条件就考虑 Skill：

- 同一流程重复使用三次以上。
- 团队多人需要统一做法。
- 流程包含多个检查步骤。
- 需要附带模板、脚本或参考文档。
- 希望被 Automation 调用。

临时任务不要急着写 Skill。

## 6. Skills、Commands、Automations 的分工

| 需求 | 首选 |
|---|---|
| 当前线程快速动作 | Command |
| 固定 SOP / 复用流程 | Skill |
| 定时或后台重复执行 | Automation |
| 外部工具连接 | MCP / Connector |
| 打包分发一组能力 | Plugin |

例子：

- `/review` 是 Command。
- `$code-review` 是 Skill。
- “每天早上 review 未合并 PR”是 Automation + Skill。

## 7. 带资源的 Skill

复杂 Skill 可以带资源：

```text
my-skill/
  SKILL.md
  references/
    checklist.md
    style-guide.md
  scripts/
    validate.js
```

原则：

- 大段参考资料放 `references/`。
- 重复性强、容易写错的逻辑放 `scripts/`。
- `SKILL.md` 只写入口、流程、路由规则和必要摘要。

带脚本的 Skill 要额外写清：

- 脚本需要什么运行时。
- 是否会写文件。
- 是否会联网。
- 失败时输出什么。
- 是否能在 Windows / macOS 都跑。

不要把一段危险脚本藏在 Skill 里让 App 自动执行。

## 8. 团队共享

团队共享方式：

| 方式 | 适合 |
|---|---|
| 项目级 `.codex/skills/` | 当前仓库专用 |
| 用户级 skills 目录 | 个人跨项目复用 |
| Plugin | 打包给团队或市场分发 |

团队 Skill 要避免写个人路径、个人 token、个人偏好。

共享前验收：

| 项 | 标准 |
|---|---|
| 触发 | 点名和自然语言都能稳定触发 |
| 输出 | 结果格式稳定，方便 Review |
| 权限 | 不默认要求危险权限 |
| 平台 | Windows / macOS 路径写法不冲突 |
| 资源 | references 和 scripts 都能按相对路径找到 |
| 维护 | 有 owner，知道谁负责更新 |

## 9. Skill 与插件

Plugin 可以携带 Skills，但 Skill 不等于 Plugin。

| 能力 | 关系 |
|---|---|
| Skill | 一个可复用工作流 |
| Plugin | 一个能力包，可以包含多个 Skills、MCP、Apps、配置 |

当你只需要一个团队审查流程，用 Skill。需要打包 GitHub 连接器、MCP、多个 Skills、配置时，才考虑 Plugin。

## 10. 常见反模式

| 反模式 | 问题 |
|---|---|
| “做高质量代码” | 不可验证 |
| 在 Skill 里写密钥 | 安全风险 |
| 把工具 API 伪装成 Skill | 应该用 MCP |
| 把长期周期任务写成 Skill | 应该用 Automation 调用 Skill |
| 一个 Skill 解决所有事 | 边界过大，难维护 |

## 常见问题

### Q1：Skills 是 slash commands 吗？

不完全是。Skill 可以被点名触发，也可能显示成命令入口，但本质是可复用工作流。

### Q2：Codex App 会自动加载所有 Skills 吗？

取决于当前会话、项目、用户目录和插件。以 App 当前可见列表为准。

### Q3：Skill 能运行脚本吗？

可以引用脚本资源，但是否执行取决于权限和当前环境。脚本要最小权限、可审计。

## 下一步

下一篇：[CX-07 Plugins / Connectors：App 能力扩展与账号连接](./CX-07-Codex-Plugins连接器完整指南.md)。
