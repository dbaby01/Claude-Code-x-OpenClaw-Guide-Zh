# Codex 沙箱安全与企业指南：审批、沙盒、权限与合规

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
> - **适用版本**：Codex CLI v0.128.0（验证于 2026-04-30）/ Codex App 当前版本
> - **信息来源**：[GitHub](https://github.com/openai/codex) | [Codex 官方文档](https://openai.com/codex)

---

> **定位说明**：本篇服务于 Codex App 安全落地。重点是 App 的审批、沙盒、Browser/Computer Use、插件/MCP、Memories 和团队约束；CLI/CI 只是安全边界的补充说明。

## 重要校准：按官方安全、记忆和配置口径写

旧稿里这些内容不要再当官方事实：

- `security_level`
- `audit_mode`
- `sandbox_enabled`
- `allowed_commands`
- `allowed_network_hosts`
- “Codex Enterprise Admin Console” 的具体 JSON 策略示例
- “Windows 使用 Windows Sandbox / Hyper-V 容器”的确定实现描述

当前教程只保留可落地的安全主线：`approval_policy`、`sandbox_mode`、workspace-write 限制、rules、hooks、MCP/插件权限、Browser/Computer Use 边界、Memories/Chronicle、企业 `requirements.toml`。

---

## 📚 本课学习目标

完成本课后，你将能够：

1. 设计 Codex 的个人和团队安全基线
2. 正确使用审批策略与沙盒模式
3. 用 rules 控制沙盒外命令
4. 用 hooks 做审计和安全检查
5. 管理 MCP、插件、浏览器和 Computer Use 的权限风险
6. 理解企业 `requirements.toml` 的作用

---

## 第1部分：安全模型

Codex 的安全不是单点开关，而是多层组合：

| 层级 | 作用 |
|------|------|
| 用户审批 | 决定何时让人确认 |
| 沙盒 | 限制命令读写和网络能力 |
| Rules | 控制哪些命令可在沙盒外自动运行 |
| Hooks | 在会话和工具调用事件上做检查/审计 |
| AGENTS.md | 给模型明确项目禁区和验证要求 |
| MCP / Plugins 权限 | 控制外部系统数据边界 |
| App Settings | Browser、Computer Use、Memories、Notifications 等 |
| 企业 requirements | 管理员强制约束 |

安全目标不是“完全不让 Codex 做事”，而是让每类动作有清晰边界、可审查、可回滚。

---

## 第2部分：审批与沙盒

### 2.1 审批策略

`approval_policy` 控制何时请求人工确认。CLI 对应：

```bash
codex --ask-for-approval untrusted
codex --ask-for-approval on-request
codex --ask-for-approval never
```

推荐：

| 场景 | 建议 |
|------|------|
| 新项目 / 不熟悉仓库 | `untrusted` |
| 日常开发 | `on-request` |
| CI runner | `never`，但必须有外部隔离 |

### 2.2 沙盒模式

```toml
sandbox_mode = "read-only"
sandbox_mode = "workspace-write"
sandbox_mode = "danger-full-access"
```

| 模式 | 风险 | 建议 |
|------|------|------|
| `read-only` | 低 | 审计、解释、调研 |
| `workspace-write` | 中 | 日常开发主力 |
| `danger-full-access` | 高 | 只在外部强隔离环境中使用 |

### 2.3 workspace-write 原则

在 workspace-write 下，Codex 应该只写项目工作区和显式允许的目录。需要访问额外目录时，用 CLI `--add-dir` 或官方支持的 workspace-write 配置，不要直接切到无沙盒。

---

## 第3部分：Rules

Rules 控制命令是否允许在沙盒外运行。规则文件是 `.rules`，位置示例：

```text
~/.codex/rules/default.rules
<repo>/.codex/rules/project.rules
```

项目本地 rules 只有 trusted project 才加载。

示意：

```text
prefix_rule(
  pattern = ["gh", "pr", "view"],
  decision = "ask",
  description = "查看 PR 前询问"
)
```

不要把旧稿里的：

```toml
[sandbox]
allowed_commands = ["node", "npm"]
```

当成官方配置。命令策略应走 rules。

---

## 第4部分：Hooks

Hooks 是事件脚本，可用于安全检查和审计。常见事件包括：

| 事件 | 用途 |
|------|------|
| `SessionStart` | 会话开始时补充上下文 |
| `PreToolUse` | 工具调用前检查 |
| `PermissionRequest` | 权限请求时参与决策 |
| `PostToolUse` | 工具调用后记录 |
| `UserPromptSubmit` | 用户提交提示后处理 |
| `Stop` | 回合结束时处理 |

适合做：

- 记录工具调用审计日志
- 阻止访问敏感路径
- 在危险命令前追加上下文
- 对输出做轻量合规检查

不适合做：

- 静默修改业务文件
- 替代测试系统
- 绕过审批

---

## 第5部分：敏感文件和密钥

### 5.1 不要依赖单个忽略文件

不要把非官方忽略文件当官方安全机制。更可靠的做法：

- `.env*` 不进 Git
- CI 使用 secret manager
- AGENTS.md 写明禁止读取/修改敏感文件
- 沙盒限制写入范围
- rules 阻止危险命令
- hooks 记录或阻断敏感路径访问

### 5.2 AGENTS.md 安全段落

```markdown
## Safety
- Do not read or modify `.env*`, private keys, credentials, or production dumps.
- Do not run destructive commands unless explicitly requested.
- Do not push branches or create PRs unless the user asks.
- Prefer read-only analysis for unknown repositories.
```

---

## 第6部分：MCP 与插件安全

### 6.1 MCP 风险

MCP server 可能让 Codex 访问：

- GitHub / GitLab
- 数据库
- Google Drive / Docs
- Slack / Teams
- 内部 API

每个 server 都应记录：

| 字段 | 内容 |
|------|------|
| 用途 | 为什么需要 |
| 权限 | 只读 / 可写 / 管理 |
| 凭据 | 从哪里加载 |
| 禁区 | 不能做什么 |
| 审计 | 如何追踪调用 |

### 6.2 插件和 Apps

插件可以包含 skills、apps、MCP servers。安装插件不会自动绕过审批，也不等于外部服务已经授权。

安全原则：

- 只安装需要的插件
- 外部 app 用最小权限授权
- 明确数据会进入第三方服务
- 离职或项目结束时撤销外部授权

---

## 第7部分：Browser 与 Computer Use

### 7.1 Browser use

Browser use 会管理允许/阻止的网站。建议：

- 开发文档、localhost、测试站点按需允许
- 生产后台、支付、银行、客户数据页面默认阻止
- 不在浏览器任务中粘贴密钥

### 7.2 Computer Use

Computer Use 涉及系统级权限和桌面操作。官方 App Settings 中强调 macOS 需要关注 Screen Recording / Accessibility 等系统权限；要撤销需到系统 Privacy & Security 处理。

建议：

- 只在必要任务中启用
- 先让 Codex 说明操作计划
- 避免支付、密码、生产后台
- 保留日志和人工确认

---

## 第8部分：Memories 与隐私

官方 Memories 文档说明：Memories 默认关闭，发布初期不在 EEA、英国、瑞士提供；可在 Codex App settings 中开启，也可以在 `~/.codex/config.toml` 里配置：

```toml
[features]
memories = true
```

开启后，Codex 可以把稳定偏好、常用工作流、技术栈、项目约定和已知坑点带到后续线程。官方文档还把 Chronicle 放在 Memories 体系下，用来帮助 Codex 从屏幕恢复近期工作上下文并形成 memory。

常见 memory 相关配置包括：

```toml
[memories]
generate_memories = true
use_memories = true
disable_on_external_context = true
min_rate_limit_remaining_percent = 20
```

企业和敏感项目建议：

- 明确哪些信息可以被记忆
- 避免让 Codex 总结密钥、客户数据、生产事故细节到长期记忆
- 使用 App Settings 管理开启/关闭
- 可用 `/memories` 控制当前线程是否使用已有 memory、是否用于生成 future memory
- 组织策略优先于个人偏好

不要把 Memories 当作团队强制规则的唯一来源。必须稳定执行的规则应放在 `AGENTS.md`、仓库文档或管理员配置里。

---

## 第9部分：企业 requirements.toml

`requirements.toml` 是管理员强制配置，用来约束用户不能覆盖的安全敏感设置。

适合企业统一规定：

- 禁止 `danger-full-access`
- 强制审批策略
- 限制网络和工作区写入
- 约束插件和 MCP
- 要求响应存储策略

企业治理文档应写清：

| 项目 | 说明 |
|------|------|
| 维护者 | 安全团队 / 平台团队 |
| 适用范围 | 哪些组织 / 项目 |
| 强制项 | 审批、沙盒、网络、插件等 |
| 例外流程 | 谁审批，如何记录 |

---

## 第10部分：推荐安全基线

### 10.1 个人日常开发

```toml
approval_policy = "on-request"
sandbox_mode = "workspace-write"
model_reasoning_effort = "medium"
```

### 10.2 只读审计

```toml
approval_policy = "on-request"
sandbox_mode = "read-only"
```

### 10.3 CI

```bash
codex exec --ask-for-approval never --sandbox workspace-write \
  "运行检查并输出报告"
```

CI 外部必须有：

- 最小权限 token
- 超时
- 不直接 push 主分支
- 不暴露生产密钥
- 日志留存

---

## FAQ

### Q1：可以彻底跳过审批和沙盒吗？

A：可以用危险参数，但只应在外部强隔离环境中使用。个人机器不建议。

### Q2：Codex 会不会读取 `.env`？

A：取决于工具权限、沙盒和任务。团队应通过 AGENTS.md、rules、hooks、secret 管理和沙盒共同限制，不要只靠口头约定。

### Q3：Memories 和 Chronicle 是官方能力吗？

A：是。官方文档当前有 Memories，并在其下列出 Chronicle。安全教程应讲清楚开启方式、存储位置、隐私边界和 `/memories` 当前线程控制，而不是发明额外的安全配置键。

### Q4：企业控制台有哪些具体字段？

A：不要在未对照官方企业文档时写具体 JSON 字段。企业强制配置以 `requirements.toml` 和官方管理员文档为准。

---

## 术语表

| 术语 | 通俗解释 |
|------|----------|
| `approval_policy` | 人工审批策略 |
| `sandbox_mode` | 命令沙盒模式 |
| Rules | 沙盒外命令策略 |
| Hooks | 事件脚本 |
| MCP | 外部工具协议 |
| Plugin | skills/apps/MCP 扩展包 |
| Memories / Chronicle | 跨线程上下文与近期工作上下文恢复能力 |
| `requirements.toml` | 企业强制配置 |

---

## 下一步

- **CX-05**：Codex 配置系统完整指南
- **CX-08**：Codex Automations 与集成完整指南
- **CX-10**：Codex 综合实战完整指南
- **CX-11**：Codex 与 Claude Code 对比实战指南
