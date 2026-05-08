# Codex 配置系统完整指南：从 AGENTS.md 到 config.toml 的全面掌控

> **课程信息**
>
> - **作者**：老金
> - **GitHub**：https://github.com/KimYx0207
> - **公众号**：老金带你玩AI
> - **X（Twitter）**：老金带你玩AI
> - **个人博客**：https://aiking.dev
> - **预计学时**：4-6小时
> - **难度等级**：⭐⭐ 基础进阶
> - **更新日期**：2026年5月
> - **适用版本**：Codex CLI v0.128.0（验证于 2026-04-30）/ Codex App 当前版本
> - **信息来源**：[GitHub](https://github.com/openai/codex) | [config.schema.json](https://github.com/openai/codex/blob/main/codex-rs/core/config.schema.json)
> - **前置课程**：CX-01 安装与认证 / CX-02 App / CX-03 CLI

---

> **定位说明**：本篇服务于 Codex App 主线。App 用户通常不需要从配置文件开始学；当你要统一项目指令、调整沙盒/审批、接 MCP、管理插件、写 rules/hooks 或做团队规范时再看。

## 重要校准：旧配置示例不要再照抄

旧稿里的很多配置项不是 Codex 官方 `config.toml` 结构，例如：

- `[core]`
- `approval_mode = "suggest"`
- `[sandbox] enabled = true`
- `allowed_read_paths`
- `allowed_network_hosts`
- `denied_paths`
- `[model] temperature / max_tokens`
- 非官方忽略文件
- `.codex/rules/*.md`
- 旧式 hooks 事件名
- `codex --speed deep`
- `codex plugins install xxx`

这些要么是旧认知，要么是其他工具的习惯，要么无法在当前官方 Codex 配置参考中证实。Codex 配置要以官方 `config.toml`、`.rules`、hooks、AGENTS.md、plugins、MCP 和企业 `requirements.toml` 为主线。

---

## 📚 本课学习目标

完成本课后，你将能够：

1. 区分 `AGENTS.md`、`config.toml`、rules、hooks、plugins、MCP 的职责
2. 正确设置 `approval_policy` 和 `sandbox_mode`
3. 配置项目级 `.codex/config.toml`，并理解 trusted project 限制
4. 编写安全的 `.rules` 文件控制沙盒外命令
5. 配置 MCP server 和插件启用状态
6. 理解 hooks 的真实事件名和加载位置
7. 为团队建立可审计的配置基线

---

## 第1部分：配置体系概览

### 1.1 五类配置文件

| 文件 / 目录 | 作用 | 适合放什么 |
|-------------|------|------------|
| `AGENTS.md` | 给 Codex 的自然语言项目指令 | 项目背景、代码规范、测试命令、禁止事项 |
| `~/.codex/config.toml` | 用户级 Codex 配置 | 默认模型、审批、沙盒、MCP、插件、profiles |
| `<repo>/.codex/config.toml` | 项目级配置 | 项目特定覆盖；只有 trusted project 才加载 |
| `rules/*.rules` | 控制哪些命令可在沙盒外运行 | allow / ask / deny 命令前缀 |
| hooks | 工具调用和会话事件的自动脚本 | 审计、额外上下文、命令前后检查 |

企业环境还可能有 `requirements.toml`，用于管理员强制约束用户不能覆盖的安全设置。

### 1.2 职责边界

| 需求 | 应该用 |
|------|--------|
| “这个项目怎么测试？” | `AGENTS.md` |
| “默认用什么模型？” | `config.toml` |
| “是否允许写文件？” | `sandbox_mode` |
| “什么时候请求人工审批？” | `approval_policy` |
| “允许 `gh pr view` 不再询问？” | `.rules` |
| “接 GitHub / 数据库 / 文档服务？” | `mcp_servers` |
| “启用/禁用 Gmail 插件？” | `plugins."id".enabled` |
| “工具调用前做安全检查？” | hooks |

不要把模型、权限、沙盒写进 `AGENTS.md` 当作硬配置。`AGENTS.md` 是指令，不是权限系统。

---

## 第2部分：AGENTS.md

### 2.1 AGENTS.md 是什么

`AGENTS.md` 是 Codex 自动读取的项目说明。它应该回答：

- 这个项目是什么
- 主要技术栈是什么
- 常用命令是什么
- 代码风格和测试要求是什么
- 哪些文件不能动
- 修改后如何验证

### 2.2 推荐模板

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
- 使用 TypeScript strict mode
- 不新增 `any`
- 复用现有组件和工具函数

## Testing
- 修改业务逻辑时补充或更新测试
- 提交前至少运行相关测试

## Safety
- 不读取或修改 `.env*`
- 不提交真实密钥
- 不直接改数据库迁移，除非任务明确要求
```

### 2.3 多层 AGENTS.md

在 monorepo 中，可以在根目录和子目录放置不同的 `AGENTS.md`。Codex 会把相关层级的项目说明纳入上下文。不要理解成“Markdown 同名章节自动覆盖”，更准确的理解是：越贴近当前文件的说明越能补充该目录的上下文。

### 2.4 写法原则

- 写可执行命令，不写“按情况测试”
- 写明确禁止事项，不写泛泛安全口号
- 写项目事实，不写模型参数
- 保持短而稳定，避免把临时任务塞进去

---

## 第3部分：config.toml 基础

### 3.1 配置位置

用户级：

```text
~/.codex/config.toml
```

项目级：

```text
<repo>/.codex/config.toml
```

项目级配置只有在该项目被信任后才会加载。这样可以避免 clone 一个陌生仓库后自动执行不可信配置。

### 3.2 VS Code / Cursor TOML schema

官方文档提供 schema。可以在 `config.toml` 顶部加入：

```toml
#:schema https://developers.openai.com/codex/config-schema.json
```

然后配合 TOML 扩展获得补全和诊断。

### 3.3 常用顶层键

下面是教程中建议优先认识的键。完整列表以官方 Config Reference 为准。

| 键 | 作用 |
|----|------|
| `model` | 默认模型 |
| `approval_policy` | 何时请求人工审批 |
| `sandbox_mode` | shell 命令运行沙盒 |
| `model_reasoning_effort` | 推理强度 |
| `model_instructions_file` | 额外模型指令文件 |
| `disable_response_storage` | 是否禁用响应存储 |
| `features.<name>` | 功能开关 |
| `mcp_servers.<id>.*` | MCP server 配置 |
| `plugins."<id>".enabled` | 插件启用/禁用 |

---

## 第4部分：审批与沙盒

### 4.1 CLI 对应参数

当前 CLI 帮助中，审批参数是：

```bash
codex --ask-for-approval untrusted
codex --ask-for-approval on-request
codex --ask-for-approval never
```

其中 `on-failure` 已在 CLI 帮助中标为 deprecated。不要再把它写成推荐策略。

沙盒参数是：

```bash
codex --sandbox read-only
codex --sandbox workspace-write
codex --sandbox danger-full-access
```

### 4.2 config.toml 写法

```toml
approval_policy = "on-request"
sandbox_mode = "workspace-write"
model_reasoning_effort = "medium"
```

常见策略：

| 场景 | `approval_policy` | `sandbox_mode` |
|------|-------------------|----------------|
| 阅读和解释代码 | `on-request` | `read-only` |
| 日常本地开发 | `on-request` | `workspace-write` |
| CI / 外部沙箱中无头运行 | `never` | `workspace-write` 或外部隔离 |
| 高风险全权限 | 谨慎 | `danger-full-access` |

### 4.3 `--full-auto` 已废弃（deprecated）

CLI 中 `--full-auto` 原本是低摩擦、沙盒化自动执行的便捷别名。**2026年4月底 CLI 发布已将其标记为 deprecated**，保留为兼容路径。

推荐的替代写法：

```bash
# 替代 --full-auto 的等效命令
codex --sandbox workspace-write --ask-for-approval on-request
```

它不等于在 `config.toml` 中写：

```toml
approval_mode = "full-auto"
```

这种旧写法不要再用。

### 4.4 `dangerously-bypass-approvals-and-sandbox`

CLI 中存在：

```bash
codex --dangerously-bypass-approvals-and-sandbox
```

它会跳过确认和沙盒。只应在外部已经有强隔离的环境中使用，例如一次性容器、受控 runner。不要在个人机器或生产仓库里当默认配置。

---

## 第5部分：Profiles

Profiles 用来保存不同配置组合，然后通过 CLI 选择：

```bash
codex --profile safe
codex --profile ci
```

示意：

```toml
[profiles.safe]
approval_policy = "on-request"
sandbox_mode = "read-only"
model_reasoning_effort = "medium"

[profiles.dev]
approval_policy = "on-request"
sandbox_mode = "workspace-write"
model_reasoning_effort = "medium"

[profiles.ci]
approval_policy = "never"
sandbox_mode = "workspace-write"
model_reasoning_effort = "low"
```

团队可以约定 profile 名称，但不要假设所有成员本机已经有同名 profile。

---

## 第6部分：MCP 配置

### 6.1 用 CLI 管理 MCP

先看本机支持的命令：

```bash
codex mcp --help
codex mcp list
codex mcp add --help
```

当前 CLI 提供：

- `list`
- `get`
- `add`
- `remove`
- `login`
- `logout`

### 6.2 config.toml 中的 MCP

MCP server 会以 `mcp_servers.<id>` 形式出现。不同 server 的字段取决于它是 stdio、HTTP、需要 env 还是需要 OAuth。

示意：

```toml
[mcp_servers.github]
command = "npx"
args = ["-y", "@modelcontextprotocol/server-github"]
env = { GITHUB_TOKEN = "$GITHUB_TOKEN" }
```

示例只表达结构。实际包名、参数、认证方式要看对应 MCP server 官方文档。

### 6.3 MCP 安全原则

- 只装当前项目需要的 server
- 使用最小权限 token
- 不把 token 明文写入仓库
- 数据库类 MCP 默认只读
- 在 `AGENTS.md` 写明每个 MCP 的用途和禁区

---

## 第7部分：Plugins 插件配置

### 7.1 插件是什么

Codex plugin 是一个扩展包，可以包含：

- Skills
- Apps / connectors
- MCP servers

安装入口包括 App 的 Plugins 页面，以及 CLI 的 plugin / marketplace 能力。当前 CLI 帮助中是：

```bash
codex plugin --help
codex plugin marketplace --help
```

不要写成未证实的：

```bash
codex plugins install context7
```

### 7.2 禁用插件

官方示例：

```toml
[plugins."gmail@openai-curated"]
enabled = false
```

禁用后重启 Codex。卸载插件则应在插件浏览器中处理。注意：卸载插件不一定等于撤销外部 app 授权，外部服务的授权需要在对应服务或 ChatGPT app 管理中处理。

### 7.3 调用插件

安装后可以自然语言使用，也可以用 `@` 指定：

```text
@gmail 总结今天未读的客户邮件
@slack 总结 #release 频道过去 24 小时的阻塞问题
```

可用插件取决于你安装了什么、组织是否允许、外部服务是否授权。

---

## 第8部分：Rules

### 8.1 Rules 控制什么

Rules 用来控制 Codex 哪些命令可以在沙盒外运行。官方规则文件是 `.rules`，放在 active config layer 旁边的 `rules/` 目录下，例如：

```text
~/.codex/rules/default.rules
<repo>/.codex/rules/project.rules
```

项目本地 rules 只有在项目 `.codex/` 层被信任后才加载。

### 8.2 rule 示例

下面是概念示例，字段以官方 Rules 文档和 schema 为准：

```text
prefix_rule(
  pattern = ["gh", "pr", "view"],
  decision = "ask",
  description = "查看 PR 详情前询问"
)
```

当你在 TUI 中把某个命令加入 allow list，Codex 会写入用户层 `~/.codex/rules/default.rules`，以便以后跳过相同提示。

### 8.3 shell 包装命令

官方 Rules 文档特别强调 shell 包装命令风险，例如：

```text
["bash", "-lc", "git add . && rm -rf /"]
```

Codex 会尽量拆分简单线性命令并分别应用 rules；遇到复杂 shell 特性时会保守处理整个调用。不要用 `bash -lc` 包一堆动作来绕过 rules。

---

## 第9部分：Hooks

### 9.1 Hooks 是什么

Hooks 是 Codex 在会话或工具调用事件上运行的脚本机制。官方文档说明 hooks 需要通过 `config.toml` feature flag 启用，且 Codex 会在 active config layers 附近查找 hooks。

### 9.2 常见事件名

旧稿里的旧式 hooks 事件名不应继续使用。官方 hooks 事件包括：

| 事件 | 大致用途 |
|------|----------|
| `SessionStart` | 会话开始时补充上下文 |
| `PreToolUse` | 工具调用前检查 |
| `PermissionRequest` | 权限请求时参与决策 |
| `PostToolUse` | 工具调用后记录或校验 |
| `UserPromptSubmit` | 用户提交提示时处理 |
| `Stop` | 回合结束或停止时处理 |

具体输入/输出 JSON 字段以官方 Hooks 文档和 schema 为准。

### 9.3 Hook 使用场景

适合：

- 给会话注入本地项目状态
- 阻止危险工具调用
- 记录审计日志
- 工具调用后运行轻量校验

不适合：

- 代替测试系统
- 在 hook 里做复杂业务逻辑
- 静默修改用户文件
- 绕过审批和沙盒

---

## 第10部分：企业 requirements.toml

`requirements.toml` 是管理员强制配置，用来约束用户不能覆盖的安全敏感项。普通个人用户通常不需要它。

适合企业统一控制：

- 禁止 `danger-full-access`
- 强制某些审批策略
- 限制网络访问
- 要求禁用某些功能
- 统一 MCP / 插件边界

企业文档中应写明：

| 项目 | 要写清楚 |
|------|----------|
| 谁维护 | 平台团队 / 安全团队 |
| 放在哪里 | 官方支持的位置 |
| 限制什么 | 审批、沙盒、网络、插件等 |
| 如何例外 | 申请流程和审计要求 |

---

## 第11部分：推荐团队基线

### 11.1 `AGENTS.md`

```markdown
# Project Instructions

## Commands
- lint: npm run lint
- typecheck: npm run typecheck
- test: npm test

## Safety
- Do not read or modify `.env*`.
- Do not push branches unless explicitly asked.
- Prefer focused tests over full test suites unless the change is broad.
```

### 11.2 `~/.codex/config.toml`

```toml
#:schema https://developers.openai.com/codex/config-schema.json

approval_policy = "on-request"
sandbox_mode = "workspace-write"
model_reasoning_effort = "medium"
```

### 11.3 `.rules`

只把高频、低风险、可解释的命令加入 allow list。删除、推送、改权限、访问生产系统等命令保持询问或禁止。

---

## FAQ

### Q1：`approval_mode = "suggest"` 还能用吗？

A：不要再按这个写。当前教程统一使用 `approval_policy`，CLI 参数是 `--ask-for-approval`。

### Q2：Codex 有官方忽略文件吗？

A：不要把它当官方机制写进教程。敏感文件保护应依赖沙盒、受保护路径、项目指令、rules 和团队安全规范。

### Q3：可以在 AGENTS.md 里指定模型吗？

A：不建议。模型属于配置层，写在 `config.toml` 或命令行参数；`AGENTS.md` 只写项目说明和工作约束。

### Q4：Rules 和 Hooks 有什么区别？

A：Rules 决定命令是否可在沙盒外运行；Hooks 是在事件上执行脚本。Rules 是权限策略，Hooks 是扩展点。

### Q5：插件配置会不会自动授权外部服务？

A：不会。插件安装、外部 app 授权、MCP server 认证是不同层次。外部服务仍需单独登录和授权。

---

## 术语表

| 术语 | 通俗解释 |
|------|----------|
| `AGENTS.md` | 给 Codex 看的项目说明 |
| `config.toml` | Codex 的 TOML 配置文件 |
| `approval_policy` | 何时请求人工审批 |
| `sandbox_mode` | shell 工具运行的沙盒级别 |
| Profile | 一组可复用配置 |
| MCP | 外部工具协议 |
| Plugin | 包含 skills/apps/MCP 的扩展包 |
| Rule | 命令执行策略 |
| Hook | 事件脚本 |
| `requirements.toml` | 企业强制配置 |

---

## 下一步

- **CX-06**：Codex Skills 技能系统完整指南
- **CX-07**：Codex MCP 与 Subagents 完整指南
- **CX-08**：Codex Automations 与集成完整指南
