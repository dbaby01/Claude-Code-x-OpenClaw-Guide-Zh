# Codex MCP 与 Subagents 完整指南：外部工具连接与多 Agent 协作

> **课程信息**
>
> - **作者**：老金
> - **GitHub**：https://github.com/KimYx0207
> - **公众号**：老金带你玩AI
> - **X（Twitter）**：老金带你玩AI
> - **个人博客**：https://aiking.dev
> - **预计学时**：3-4小时
> - **难度等级**：⭐⭐⭐ 进阶
> - **更新日期**：2026年5月
> - **适用版本**：Codex CLI v0.129.0（核对于 2026-05-08；以 `codex --version` 和 GitHub Releases 为准）/ Codex App 当前版本
> - **信息来源**：[Codex MCP 文档](https://developers.openai.com/codex/mcp) | [Codex CLI 文档](https://developers.openai.com/codex/cli/) | [Codex GitHub](https://github.com/openai/codex)
> - **前置课程**：CX-01 安装 / CX-03 CLI / CX-05 配置系统

---

> **定位说明**：本篇是 Codex App 外部工具和多 Agent 能力补充。只用 App 做本地代码任务时可以先跳过；当你要接数据库、浏览器、文档源、GitHub、内部工具，或让多个 Agent 并行处理任务时再看。

## 📚 本课学习目标

完成本课学习后，你将能够：

1. 理解 MCP 在 Codex 里的角色：让 Codex 连接外部工具和数据源
2. 用 `codex mcp` 或 `config.toml` 配置 MCP Server
3. 理解 `codex app-server` 与 MCP Server 的区别
4. 使用 Codex Subagents 做并行探索、审查和实现
5. 编写项目级自定义 Agent 配置
6. 判断哪些扩展能力适合用 MCP、Skills、Plugins 或 Subagents

---

## 第1部分：MCP 的定位

### 1.1 MCP 是什么

MCP（Model Context Protocol）是一个开放协议，用来把 AI Agent 连接到外部工具、服务和数据源。

在 Codex 里，MCP 最常见的角色是：

| 角色 | 说明 | 示例 |
|------|------|------|
| Codex 作为 MCP Client | Codex 调用外部 MCP Server 提供的工具 | Context7 文档、Figma、Playwright、Sentry |
| 外部产品使用 Codex 能力 | 通常走 Codex SDK 或 `codex app-server`，不是普通 MCP 工具清单 | 自研 IDE、内部平台、自动化系统 |

不要把 MCP 理解成“万能插件系统”。如果只是写一组可复用工作流程，优先用 Skills；如果要打包 skills、MCP servers、apps 等能力，优先看 Plugins；如果要让多个 Agent 并行处理任务，用 Subagents。

### 1.2 Codex 支持的 MCP Server 类型

Codex 当前支持两类常见 MCP Server：

| 类型 | 配置方式 | 适合场景 |
|------|----------|----------|
| STDIO | 本地启动一个命令，由 Codex 与其标准输入输出通信 | `npx -y @upstash/context7-mcp` |
| Streamable HTTP | 连接到一个 HTTP 地址 | 远程 Figma MCP、本地 Chrome DevTools MCP |

---

## 第2部分：配置 MCP Server

### 2.1 推荐方式：用 CLI 管理

```bash
codex mcp --help
codex mcp list
codex mcp add context7 -- npx -y @upstash/context7-mcp
codex mcp get context7
codex mcp remove context7
```

支持 OAuth 的 MCP Server 可以使用：

```bash
codex mcp login <server-name>
codex mcp logout <server-name>
```

在 Codex CLI 的交互界面里，可以用 `/mcp` 查看当前可用的 MCP Server。

### 2.2 配置文件方式

Codex 的 MCP 配置放在 `config.toml` 里：

| 作用域 | 文件 |
|--------|------|
| 用户级 | `~/.codex/config.toml` |
| 项目级 | `<repo>/.codex/config.toml`，只在 trusted project 中生效 |

STDIO 示例：

```toml
[mcp_servers.context7]
command = "npx"
args = ["-y", "@upstash/context7-mcp"]
env_vars = ["LOCAL_TOKEN"]

[mcp_servers.context7.env]
MY_ENV_VAR = "MY_ENV_VALUE"
```

HTTP 示例：

```toml
[mcp_servers.figma]
url = "https://mcp.figma.com/mcp"
bearer_token_env_var = "FIGMA_OAUTH_TOKEN"
http_headers = { "X-Figma-Region" = "us-east-1" }
```

限制工具示例：

```toml
[mcp_servers.chrome_devtools]
url = "http://localhost:3000/mcp"
enabled_tools = ["open", "screenshot"]
startup_timeout_sec = 20
tool_timeout_sec = 45
enabled = true
```

### 2.3 常见错误写法

下面这些写法不要再沿用：

```toml
[mcp]
servers = [
  { name = "github", command = "mcp-server-github" }
]
```

```json
// 旧教程里的独立 JSON 配置入口示例
{
  "servers": {}
}
```

Codex 当前官方配置入口是 `config.toml` 的 `[mcp_servers.<name>]` 表，或 `codex mcp` 子命令。

### 2.4 安全边界

MCP Server 能给 Agent 暴露额外工具，所以配置时要注意：

- 只连接可信来源的 MCP Server。
- 不要把长期密钥直接写进仓库文件，优先用环境变量或 OAuth。
- 给高风险工具限制 `enabled_tools`，或者为相关 Agent 设置 `sandbox_mode = "read-only"`。
- 项目级 `.codex/config.toml` 只应在可信仓库中启用。

---

## 第3部分：Codex App Server 不是普通 MCP Server

Codex CLI 里有 `codex app-server`，它用于把 Codex 嵌入到更复杂的产品或客户端中，例如 IDE 扩展、自研开发平台等。

它和“连接一个 MCP Server”不是同一件事：

| 能力 | 用什么 |
|------|--------|
| 让 Codex 使用外部工具 | MCP Server |
| 在 CI 或脚本里自动运行 Codex 任务 | `codex exec` 或 Codex SDK |
| 自己做一个 Codex 客户端，管理线程、审批、流式事件 | `codex app-server` |

启动方式：

```bash
codex app-server
```

默认传输是 `stdio`。也可以使用实验性的 WebSocket：

```bash
codex app-server --listen ws://127.0.0.1:4500
```

WebSocket 模式仍属于实验/不稳定接口；不要把非本地监听地址直接暴露到公网。如果确实要给内部产品接入，必须配置认证，并以当前版本生成的 schema 为准：

```bash
codex app-server generate-ts --out ./schemas
codex app-server generate-json-schema --out ./schemas
```

旧教程里常见的 serve 子命令和“设置里启用 App Server”说法不是当前官方写法。

---

## 第4部分：Subagents

### 4.1 什么是 Subagents

Codex 可以在你明确要求时生成多个子 Agent，并行做探索、审查、实现或验证，然后把结果汇总回主线程。

典型用途：

- 大 PR 审查：安全、正确性、测试、可维护性分别交给不同 Agent。
- 代码库探索：让多个 explorer 同时查不同模块。
- 分片实现：把互不重叠的文件或模块交给不同 worker。
- 批量分析：把 CSV 中的多行任务分派给多个 Agent，当前仍偏实验。

Codex 不会在普通任务里无条件自动开很多子 Agent。你需要明确说“spawn agents / 派多个 Agent / 并行检查这些点”。

### 4.2 内置 Agent 类型

官方当前包含这些内置 Agent：

| Agent | 适合任务 |
|-------|----------|
| `default` | 通用后备 Agent |
| `worker` | 实现、修复、执行型任务 |
| `explorer` | 只读代码库探索、证据收集 |

示例提示：

```text
请 review 当前分支相对 main 的改动。开 3 个 subagents：
1. explorer：梳理影响到的核心调用链
2. reviewer：找 correctness/security/test 风险
3. docs_researcher：通过文档核对涉及的框架 API
最后把三份结果合并成一个审查报告。
```

### 4.3 Subagents 的审批和沙箱

Subagents 继承父线程当前的审批和沙箱策略。也就是说：

- 主线程是 `read-only`，子 Agent 默认也不能写文件。
- 主线程是 `workspace-write`，子 Agent 可在允许范围内编辑。
- 需要审批的命令仍会触发审批流程。
- 非交互模式下，如果需要新的人工审批但无法弹出审批，相关动作会失败并回报给父线程。

多 Agent 会消耗更多 token 和本机资源。大任务适合并行，小任务没必要硬拆。

---

## 第5部分：自定义 Agents

### 5.1 全局限制

Subagent 全局设置放在 `config.toml` 的 `[agents]` 表：

```toml
[agents]
max_threads = 6
max_depth = 1
```

常用字段：

| 字段 | 说明 |
|------|------|
| `agents.max_threads` | 同时打开的 Agent 线程上限，默认通常为 6 |
| `agents.max_depth` | 子 Agent 嵌套深度，默认通常为 1 |
| `agents.job_max_runtime_seconds` | 批量任务中单个 worker 的默认超时 |

一般不要提高 `max_depth`。递归派发很容易导致成本、延迟和结果质量失控。

### 5.2 自定义 Agent 文件

个人级 Agent 放在：

```text
~/.codex/agents/
```

项目级 Agent 放在：

```text
<repo>/.codex/agents/
```

每个 TOML 文件定义一个 Agent，必须包含：

- `name`
- `description`
- `developer_instructions`

示例：

```toml
name = "reviewer"
description = "PR reviewer focused on correctness, security, and missing tests."
developer_instructions = """
Review code like an owner.
Prioritize correctness, security, behavior regressions, and missing test coverage.
Lead with concrete findings and cite files.
"""
nickname_candidates = ["Atlas", "Delta", "Echo"]
sandbox_mode = "read-only"
model_reasoning_effort = "high"
```

自定义 Agent 还可以包含普通 `config.toml` 支持的配置，例如 `model`、`model_reasoning_effort`、`sandbox_mode`、`mcp_servers` 和 `skills.config`。

### 5.3 带 MCP 的文档核对 Agent

```toml
name = "docs_researcher"
description = "Verify framework and API behavior through documentation MCP servers."
model_reasoning_effort = "medium"
sandbox_mode = "read-only"
developer_instructions = """
Use documentation MCP tools to confirm APIs, options, and version-specific behavior.
Return concise answers with source references.
Do not edit project files.
"""

[mcp_servers.openaiDeveloperDocs]
url = "https://developers.openai.com/mcp"
```

然后在主线程里说：

```text
请让 docs_researcher 核对这个 API 是否仍然有效，再让 worker 根据结论修正实现。
```

---

## 第6部分：和 Claude Code 共用工具链

Codex 和 Claude Code 都可以连接 MCP Server，但不要直接复制彼此的完整配置文件。正确做法是：

1. 确认目标 MCP Server 支持 STDIO 或 HTTP。
2. 在 Codex 里按 `[mcp_servers.<name>]` 或 `codex mcp add` 配置。
3. 在 Claude Code 里按 Claude Code 自己的 MCP 配置格式配置。
4. 密钥统一放环境变量或凭据管理器，不要散落在多个仓库配置里。

能共用的是“同一个 MCP Server”，不是“同一个配置文件语法”。

---

## 第7部分：选择指南

| 需求 | 推荐能力 |
|------|----------|
| 让 Codex 访问外部工具/数据 | MCP |
| 保存一套可复用工作流程 | Skills |
| 打包 skills、apps、MCP servers 等能力 | Plugins |
| 同一任务拆给多个 Agent 并行处理 | Subagents |
| 把 Codex 嵌入自研客户端 | `codex app-server` |
| CI、脚本、无人值守任务 | `codex exec` 或 Codex SDK |

---

## 关键纠错清单

- MCP 配置是 `[mcp_servers.<name>]`，不是 `[mcp] servers = [...]`。
- 没有通用的独立 MCP JSON 配置入口。
- 当前 CLI 使用 `codex app-server`，不是旧教程里的 serve 子命令。
- `codex app-server` 面向富客户端协议，不等于普通 MCP Server。
- Subagents 是当前 Codex 支持的能力，但需要用户明确请求；自定义 Agent 用 `.codex/agents/*.toml` 或 `~/.codex/agents/*.toml`。
- 子 Agent 继承父线程的审批和沙箱策略，不应把它当成绕过安全策略的方式。

---

**下一课**：CX-08 Codex Automations 与集成完整指南
