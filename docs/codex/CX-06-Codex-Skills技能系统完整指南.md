# Codex Skills 技能系统完整指南：可复用工作流与团队 SOP

> **课程信息**
>
> - **作者**：老金
> - **GitHub**：https://github.com/KimYx0207
> - **公众号**：老金带你玩AI
> - **X（Twitter）**：老金带你玩AI
> - **个人博客**：https://aiking.dev
> - **预计学时**：3-5小时
> - **难度等级**：⭐⭐⭐ 进阶
> - **更新日期**：2026年5月
> - **适用版本**：Codex CLI v0.128.0（验证于 2026-04-30）/ Codex App 当前版本
> - **信息来源**：[GitHub](https://github.com/openai/codex) | [Codex 官方文档](https://openai.com/codex)
> - **前置课程**：CX-01 安装 / CX-02 App / CX-03 CLI / CX-05 配置系统

---

> **定位说明**：本篇是 Codex App 的可复用工作流补充。只会日常聊天和改代码的用户可以先跳过；当你想把固定审查流程、发布流程、写作规范或团队 SOP 变成 App 可调用能力时再看。

## 📚 本课学习目标

完成本课学习后，你将能够：

1. 理解 Skills 的定位：把可复用流程、约束和上下文写成 `SKILL.md`
2. 区分 Skills、Plugins、MCP、Hooks 和 Subagents
3. 使用个人级、项目级和插件提供的 Skills
4. 编写自定义 Skill，并让团队通过 Git 共享
5. 避免把不存在的“内置 Skill 清单”写进教程或项目规范

---

## 第1部分：Skills 是什么

### 1.1 一句话解释

Skill 是一份可复用的 Agent 工作说明。它通常由一个 `SKILL.md` 文件和可选的脚本、模板、参考资料组成。

当你在 Codex 里点名一个 Skill，或任务明显匹配它的描述时，Codex 会读取对应 `SKILL.md`，按里面的流程执行。

### 1.2 Skills 适合解决什么问题

| 场景 | 是否适合 Skill |
|------|----------------|
| 固定的代码审查清单 | 适合 |
| 公司内部发布流程 | 适合 |
| 文档核对流程 | 适合 |
| 需要调用外部 API 的实时工具 | 通常更适合 MCP 或 Plugin |
| 命令执行前强制拦截 | 更适合 Rules 或 Hooks |
| 多 Agent 并行处理 | 更适合 Subagents |

Skill 的重点不是“给模型装一个新能力”，而是把你希望 Agent 每次都遵守的流程、判断标准、文件结构和输出格式写清楚。

---

## 第2部分：Skills、Plugins、MCP、Hooks 的区别

| 能力 | 本质 | 触发方式 | 常见用途 |
|------|------|----------|----------|
| Skills | Markdown 指令 + 可选资源 | 用户点名、描述匹配、插件提供 | 工作流、审查规范、写作规范 |
| Plugins | 能力包 | 安装后可自然调用或用 `@plugin` 点名 | 打包 skills、apps、MCP servers |
| MCP | 外部工具协议 | Agent 需要工具时调用 | GitHub、浏览器、数据库、文档源 |
| Hooks | 事件脚本 | SessionStart、PreToolUse、PostToolUse 等事件 | 日志、检查、通知、策略集成 |
| Rules | 命令审批策略 | 命令匹配时触发 | 限制危险命令、设置审批规则 |

旧教程里把 Figma、Linear、Cloudflare、Netlify 等写成“官方内置 Skills 清单”，这种说法不严谨。它们更可能以 App、MCP Server、Plugin、连接器或第三方 Skill 的形式出现，是否可用取决于当前安装和登录状态。

---

## 第3部分：Skill 的文件结构

### 3.1 最小结构

```text
.codex/skills/code-review/
└── SKILL.md
```

`SKILL.md` 示例：

```markdown
---
name: code-review
description: Review the current diff for correctness, security, regressions, and missing tests.
---

# Code Review

## Workflow

1. Inspect the current git diff and affected files.
2. Identify correctness, security, behavioral regression, and missing-test risks.
3. Lead with findings ordered by severity.
4. Cite file paths and line numbers where possible.

## Output

- Findings first.
- Open questions second.
- Brief summary last.

## Rules

- Do not rewrite unrelated code.
- Do not claim tests passed unless you ran them.
```

### 3.2 带资源的 Skill

```text
.codex/skills/release-writer/
├── SKILL.md
├── templates/
│   └── release-note.md
├── references/
│   └── changelog-style.md
└── scripts/
    └── collect-commits.ps1
```

推荐原则：

- `SKILL.md` 写清楚何时使用、按什么步骤做、输出什么。
- `templates/` 放可复用模板。
- `references/` 放需要按需读取的长文档，不要在 `SKILL.md` 里塞太多正文。
- `scripts/` 放重复性强、容易写错的命令逻辑。

### 3.3 常见位置

| 位置 | 用途 |
|------|------|
| `~/.codex/skills/` | 个人通用 Skills |
| `<repo>/.codex/skills/` | 项目级 Skills |
| Plugin 包内 | 插件提供的 Skills |

具体可用 Skills 取决于当前 Codex 会话加载了哪些路径、插件和连接器。写教程时不要假设所有用户都有相同 Skill 清单。

---

## 第4部分：如何调用 Skill

### 4.1 点名调用

```text
$code-review 请审查当前分支相对 main 的改动
```

或：

```text
使用 code-review skill 检查这次改动
```

Codex App 也可能在输入框里提供上下文感知建议，允许你选择可用的 Skills、文件、插件或连接器。

### 4.2 自然语言触发

如果 Skill 的 `description` 写得足够明确，Codex 可以在用户需求明显匹配时使用它。例如：

```text
帮我按我们项目的发布模板写一版 release notes
```

如果当前项目有 `release-writer` Skill，且描述匹配，Codex 就可以使用。

### 4.3 不要依赖旧式斜杠命令表

旧文档里常见的 `/figma`、`/linear`、`/generate-image`、`/pdf` 等固定命令表不要作为 Codex 通用教程来写。不同版本、插件和宿主环境暴露的入口不一样。

更稳妥的写法是：

- 用 `$skill-name` 或自然语言点名 Skills。
- 用 `@plugin` 或自然语言点名插件。
- 用 MCP/连接器时，按当前 App/CLI 的集成界面或配置文件启用。

---

## 第5部分：编写自定义 Skill

### 5.1 写好 metadata

```markdown
---
name: tutorial-verifier
description: Verify technical tutorials against official docs and local tool behavior, then patch inaccurate sections.
---
```

`description` 很重要。它决定 Codex 能不能在用户没有显式点名时判断这个 Skill 是否适用。

### 5.2 写清楚工作流

```markdown
# Tutorial Verifier

Use when the user asks to check or update a technical tutorial for accuracy.

## Workflow

1. Identify claims that are version-sensitive or easy to hallucinate.
2. Verify them against official docs, local CLI help, or source code.
3. Patch the tutorial directly.
4. Preserve accurate teaching structure, but remove invented APIs, fake config keys, and unverified pricing/model claims.
5. Run a keyword scan for risky leftovers.

## Output

Report changed files, verification sources, and any remaining uncertainty.
```

### 5.3 把脚本变成资源，不要把脚本逻辑写死

如果 Skill 需要跑一组固定检查，可以把脚本放进 `scripts/`：

```text
.codex/skills/tutorial-verifier/
├── SKILL.md
└── scripts/
    └── scan-risky-claims.ps1
```

`SKILL.md` 里只需要说明什么时候运行脚本、如何解释结果。这样团队维护成本更低。

---

## 第6部分：团队共享

项目级 Skill 适合放进仓库：

```text
my-repo/
├── .codex/
│   └── skills/
│       └── release-writer/
│           └── SKILL.md
├── AGENTS.md
└── package.json
```

团队共享时建议：

- `name` 保持短、稳定、可读。
- `description` 说明触发条件，不要写成营销文案。
- 避免把个人路径、个人 token、机器特定命令写进 Skill。
- 涉及危险命令时，让 Skill 要求先解释影响和验证范围。
- 更新 Skill 后在 PR 里说明它会改变哪些 Agent 行为。

---

## 第7部分：图像、文档和浏览器能力怎么写

图像生成、PDF/Excel/Word 处理、浏览器自动化这类能力不要一概写成“Skills 内置功能”。更准确的写法是：

| 能力 | 常见来源 |
|------|----------|
| 图像生成 | Codex App 的 `$imagegen` Skill、宿主工具或插件 |
| PDF/Excel/Word | 当前运行环境提供的库、插件、连接器或 Skill |
| 浏览器自动化 | Codex App Browser use、MCP Browser、Playwright、插件 |
| Figma/Linear/GitHub | App/连接器、MCP Server、插件或第三方 Skill |

因此教程应该教用户先看当前 Codex 会话“可用 Skills / Plugins / Connectors”，再决定调用方式。

---

## 第8部分：最佳实践

### 8.1 好 Skill 的特征

- 触发条件明确。
- 工作流短而可执行。
- 输出格式稳定。
- 会说明哪些事不应该做。
- 需要额外资料时按需读取，不一次性塞进上下文。
- 需要脚本时复用 `scripts/`，不靠模型每次手写命令。

### 8.2 常见反模式

- 把不存在的内置命令写成事实。
- 在 Skill 里硬编码密钥、私有路径或个人账号。
- 让 Skill 同时做审查、实现、部署、写文章，范围太大。
- 把“模型应该更聪明”当成 Skill 内容，而不是写具体流程。
- 在教程里写固定版本、固定价格、固定模型，后续很快过期。

---

## 关键纠错清单

- Skills 不是固定的斜杠命令大全。
- Figma、Linear、Cloudflare 等不应被笼统称为“Codex 官方内置 Skills”。
- 图像生成不要写成旧品牌或旧模型的固定入口；以当前 Codex App 暴露的 `$imagegen` 为准，官方 App Features 文档当前说明内置图片生成使用 `gpt-image-2`。
- Plugins 可以携带 Skills，但 Skills 不等于 Plugins。
- 写自定义 Skill 时，核心是 `SKILL.md` 的触发条件、流程、资源和输出约束。

---

**下一课**：CX-07 Codex MCP 与 Subagents 完整指南
