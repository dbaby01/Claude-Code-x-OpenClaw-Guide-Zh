# Codex CLI 终端 Agent 完整指南：命令行下的 AI 编程助手

> **课程信息**
>
> - **作者**：老金
> - **GitHub**：https://github.com/KimYx0207
> - **公众号**：老金带你玩AI
> - **X（Twitter）**：老金带你玩AI
> - **个人博客**：https://aiking.dev
> - **预计学时**：3-4小时
> - **难度等级**：⭐⭐ 基础进阶
> - **更新日期**：2026年5月
> - **适用版本**：Codex CLI v0.129.0（核对于 2026-05-08；以 `codex --version` 和 GitHub Releases 为准）
> - **信息来源**：[Codex CLI 文档](https://developers.openai.com/codex/cli/) | [Codex GitHub](https://github.com/openai/codex)

---

> **定位说明**：本篇是 Codex App 主线的补充资料。只使用 App 的用户不需要先学 CLI；当你要排查 App 共享配置、MCP、插件、非交互任务或 CI 行为时再看本篇。

## 重要校准：CLI 使用当前帮助里的审批和沙箱参数

旧稿里的审批参数已经不应作为当前教程主线。当前 CLI 帮助中关键参数是：

```bash
--ask-for-approval untrusted|on-request|never
--sandbox read-only|workspace-write|danger-full-access
--full-auto
--dangerously-bypass-approvals-and-sandbox
```

`--full-auto` 是便捷别名，不是 `config.toml` 里的 `approval_mode = "full-auto"`。**注意：2026年4月底 CLI 发布已将 `--full-auto` 标记为 deprecated，建议改用 `--sandbox workspace-write` + 显式审批策略。**配置文件主线见 CX-05。

---

## 📚 本课学习目标

完成本课后，你将能够：

1. 用交互模式和非交互模式运行 Codex CLI
2. 正确设置审批策略和沙盒
3. 使用 `exec`、`review`、`mcp`、`plugin`、`app` 等子命令
4. 在 CI 中安全运行 Codex
5. 理解 CLI 与 App / IDE / Web 的边界

---

## 第1部分：CLI 基础

### 1.1 启动方式

```bash
# 交互模式
codex

# 带初始提示
codex "解释这个项目的目录结构"

# 指定工作目录
codex --cd /path/to/project "运行最小测试并总结结果"

# 指定模型（具体模型以你的账号可用项为准）
codex --model <model> "修复这个 TypeScript 错误"
```

### 1.2 查看帮助

```bash
codex --help
codex exec --help
codex review --help
codex mcp --help
codex plugin --help
```

CLI 迭代很快，写脚本前先看本机帮助，不要复制旧教程参数。

### 1.3 常见子命令

| 命令 | 用途 |
|------|------|
| `codex` | 交互式 TUI |
| `codex exec` | 非交互执行任务 |
| `codex review` | 非交互代码审查 |
| `codex login/logout` | 管理认证 |
| `codex mcp` | 管理 MCP server |
| `codex plugin` | 管理插件 marketplace |
| `codex mcp-server` | 以 stdio 启动 MCP server |
| `codex app` | 启动桌面 App 或安装引导 |
| `codex sandbox` | 在 Codex 沙盒中运行命令 |
| `codex resume/fork` | 恢复或 fork 历史会话 |

---

## 第2部分：审批与沙盒

### 2.1 审批策略

```bash
codex --ask-for-approval untrusted
codex --ask-for-approval on-request
codex --ask-for-approval never
```

| 策略 | 含义 | 适合 |
|------|------|------|
| `untrusted` | 只自动运行可信命令，不可信命令询问 | 陌生项目、保守默认 |
| `on-request` | 由模型判断何时请求审批 | 日常交互开发 |
| `never` | 不请求审批，失败直接返回给模型 | CI / 外部沙箱 |

`on-failure` 在当前帮助中是 deprecated，不建议写进新教程。

### 2.2 沙盒模式

```bash
codex --sandbox read-only
codex --sandbox workspace-write
codex --sandbox danger-full-access
```

| 模式 | 含义 | 适合 |
|------|------|------|
| `read-only` | 只读，不写文件 | 解释、审计、查错 |
| `workspace-write` | 可写工作区 | 日常修复、重构 |
| `danger-full-access` | 无文件系统沙盒 | 只在外部强隔离环境使用 |

### 2.3 便捷模式和危险模式

```bash
# ⚠️ 已废弃（deprecated），建议改用下方替代写法
codex --full-auto

# ✅ 推荐替代写法（等效行为）
codex --sandbox workspace-write --ask-for-approval on-request
```

`--full-auto` 原本是低摩擦、沙盒化自动执行的便捷模式。**2026年4月底 CLI 发布已将其标记为 deprecated**，保留为兼容路径。推荐使用 `--sandbox workspace-write` 配合显式审批策略替代。

```bash
codex --dangerously-bypass-approvals-and-sandbox
```

这个参数会跳过审批和沙盒。只有在一次性容器、隔离 VM、受控 runner 中才考虑使用。

---

## 第3部分：常用参数

| 参数 | 说明 |
|------|------|
| `-m, --model <MODEL>` | 指定模型 |
| `-C, --cd <DIR>` | 指定工作目录 |
| `--add-dir <DIR>` | 额外可写目录 |
| `-p, --profile <NAME>` | 使用 config profile |
| `-c, --config key=value` | 临时覆盖配置 |
| `--search` | 启用 live web search |
| `--image <FILE>` | 附加图片 |
| `--no-alt-screen` | 不使用终端 alternate screen |

示例：

```bash
codex -C . --sandbox workspace-write --ask-for-approval on-request \
  "修复当前项目的 lint 错误，只改必要文件"
```

---

## 第4部分：非交互模式

### 4.1 `codex exec`

```bash
codex exec "运行 npm test，并总结失败原因"
```

从 stdin 读取：

```bash
cat /tmp/errors.txt | codex exec "根据这些错误定位最可能的根因"
```

JSONL 事件输出：

```bash
codex exec --json "检查项目结构"
```

保存最后一条消息：

```bash
codex exec -o /tmp/codex-result.md "生成迁移风险报告"
```

### 4.2 `codex review`

```bash
# 审查未提交改动
codex review --uncommitted

# 审查相对 main 的改动
codex review --base main

# 审查某个 commit
codex review --commit <sha>
```

可以附加自定义审查要求：

```bash
codex review --base main "重点检查安全问题、数据迁移风险和缺失测试"
```

---

## 第5部分：CI/CD 用法

### 5.1 安全原则

- 用 API Key / CI secret，不用个人本地登录态
- 设置 `timeout-minutes`
- 默认 `--sandbox workspace-write`，不要默认无沙盒
- `--ask-for-approval never` 只适合外部 runner
- 不自动 push 到受保护分支
- 对自动修复走 PR 分支和人工 review

### 5.2 PR 审查示例

```yaml
name: Codex Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    timeout-minutes: 20

    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Install Codex CLI
        run: npm install -g @openai/codex

      - name: Run review
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
        run: |
          codex review --base origin/${{ github.base_ref }} \
            "输出中文审查意见。重点检查 bug、安全风险和缺失测试。" \
            > /tmp/codex-review.md
```

这只是 CLI 方案。若使用官方 GitHub Action，请按官方 action 文档配置，不要把旧稿里的手写流程当唯一方式。

---

## 第6部分：MCP 与插件

### 6.1 MCP

```bash
codex mcp list
codex mcp add --help
codex mcp login <server>
codex mcp logout <server>
```

MCP 配置通常写入 `config.toml`，详见 CX-05。

### 6.2 插件

当前 CLI 有：

```bash
codex plugin --help
codex plugin marketplace --help
```

插件主入口也可以在 Codex App 的 Plugins 页面。安装后可用 `@plugin` 或自然语言调用。不要写成未证实的 `codex plugins install xxx`。

---

## 第7部分：交互命令与会话管理

### 7.1 Slash commands

Codex CLI 的交互界面支持 slash commands。不同版本会增减，进入 TUI 后以 `/help` 和官方 Slash Commands 文档为准。

常见命令：

| 命令 | 用途 |
|------|------|
| `/help` | 查看当前可用命令 |
| `/model` | 查看或切换可用模型 |
| `/approvals` | 调整审批策略 |
| `/mcp` | 查看 MCP server 状态 |
| `/status` | 查看当前会话、模型、工作目录等状态 |
| `/diff` | 查看当前改动摘要 |
| `/compact` | 压缩上下文 |
| `/new` | 新建会话 |

不要把 slash commands 写成永远固定的完整表。教程可以列常用命令，但应提醒用户以当前 CLI 为准。

### 7.2 恢复与分叉

Codex CLI 提供会话恢复和分叉能力，适合：

- 继续上次没做完的任务
- 从某个历史点尝试另一种方案
- 把同一上下文拆成两个方向验证

常用命令：

```bash
codex resume --help
codex fork --help
```

使用建议：

- 继续同一条实现线，用 `resume`。
- 想试另一种设计，但不想污染当前线程，用 `fork`。
- 重要任务在分叉前先确认 git 状态，避免多个线程同时改同一文件。

### 7.3 Git 与 patch

Codex CLI 还有一些面向代码改动的辅助能力：

```bash
codex apply --help
codex review --help
```

`codex apply` 用于应用 patch；`codex review` 用于审查当前或指定分支的改动。实际参数以本机 help 为准。

### 7.4 Cloud 相关命令

当前 CLI 也包含 cloud 相关入口：

```bash
codex cloud --help
```

Cloud 能力和账号/组织设置强相关。不要把本地 CLI 文档写成“所有人都能直接云端运行”的固定承诺。

---

## 第8部分：SDK、App Server 与 MCP Server

### 8.1 SDK 适合什么

如果你要在自己的程序里创建 Codex 线程、处理事件、接入审批 UI 或构建内部自动化，应看官方 Codex SDK，而不是让脚本反复拼 `codex exec` 字符串。

当前官方 TypeScript 包名：

```bash
npm install @openai/codex-sdk
```

适合 SDK 的场景：

- 内部开发平台
- 自定义任务队列
- PR 审查服务
- 和工单/聊天/监控系统联动
- 自己实现审批、日志、重试、权限控制

### 8.2 App Server

`codex app-server` 面向“富客户端协议”，用于让自研客户端和 Codex 通信。它不是普通 MCP server，也不是旧教程里的 `serve` 子命令。

常用查看方式：

```bash
codex app-server --help
codex app-server generate-ts --help
codex app-server generate-json-schema --help
```

如果你不是在做 IDE、桌面端或内部平台，通常不需要直接用 `app-server`。

### 8.3 MCP Server

`codex mcp-server` 是另一个方向：让 Codex 以 MCP server 形式暴露给支持 MCP 的宿主。

```bash
codex mcp-server --help
```

不要把三者混淆：

| 能力 | 面向谁 |
|------|--------|
| `codex exec` | shell / CI |
| Codex SDK | 你的程序 |
| `codex app-server` | 自研富客户端 |
| `codex mcp-server` | MCP 宿主 |

---

## 第9部分：沙盒实务

### 9.1 平台差异

| 平台 | 说明 |
|------|------|
| macOS | 依赖系统沙盒能力限制文件/网络 |
| Linux | 常见依赖 bubblewrap 等隔离能力 |
| Windows | Codex App/CLI 有对应 Windows 沙盒实现，细节以官方 Windows 文档为准 |

不要把 Windows 简化写成“Windows Sandbox = Hyper-V 容器”，官方实现可能随版本变化。

### 9.2 配置写法

配置层使用：

```toml
approval_policy = "on-request"
sandbox_mode = "workspace-write"
```

更细的 workspace-write 选项、网络访问、受保护路径，按 CX-05 和官方 Config Reference 配置。

---

## FAQ

### Q1：为什么旧稿里的审批参数不能用？

A：当前 CLI 主线参数是 `--ask-for-approval` 和 `--sandbox`。旧稿参数需要更新。

### Q2：CI 里能不能直接无沙盒？

A：不建议。只有当 CI runner 本身是一次性、最小权限、无敏感凭据的外部沙箱时，才考虑危险模式。

### Q3：Slash 命令有哪些？

A：进入 CLI 后用 `/help` 看当前列表。常见有 `/help`、`/model`、`/approvals`、`/mcp`、`/status`、`/diff`、`/compact`、`/new` 等；完整列表以当前 CLI 和官方 Slash Commands 文档为准。

### Q4：App 和 CLI 历史会共享吗？

A：取决于当前版本、登录状态和工作目录。不要依赖未验证的共享行为做团队流程。

---

## 术语表

| 术语 | 通俗解释 |
|------|----------|
| TUI | 终端交互界面 |
| `approval_policy` | 何时请求人工审批 |
| `sandbox_mode` | 命令运行的沙盒级别 |
| `codex exec` | 非交互任务 |
| `codex review` | 非交互代码审查 |
| `codex app-server` | 自研 Codex 客户端协议服务 |
| `codex mcp-server` | 以 MCP server 方式暴露 Codex |
| MCP | 外部工具协议 |
| Plugin | 扩展包，可包含 skills/apps/MCP |

---

## 下一步

- **CX-05**：Codex 配置系统完整指南
- **CX-07**：Codex MCP 与 Subagents 完整指南
- **CX-08**：Codex Automations 与集成完整指南
