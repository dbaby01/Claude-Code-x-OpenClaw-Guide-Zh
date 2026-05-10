# 更新日志（Changelog）

本项目所有重要变更均记录在此文件中。

---

## [v2.1.134] - 2026-05-10

### 修改

- Claude Code 02 基础使用：新增 `CLAUDE.md` 实操入口，并将模板调整为可验证、少改动、简单优先的项目协作规则
- Claude Code 03 Commands：将内置命令速查调整为练习后的参考内容，降低“命令解读表”阅读压力
- Codex CX-04：新增 `AGENTS.md` 实操入口，并补充参考来源与项目级协作规则模板
- OpenClaw 03 快速开始：强化“先跑通第一个对话”的实操目标，并将 Control UI 介绍后置
- OpenClaw 06 技能系统：新增低风险技能练习，帮助读者先看到技能对助手行为的影响
- 将多篇教程前置的差量更新 / 版本基线说明移动到文末附录，避免打断正文学习路径

---

## [v2.1.133] - 2026-05-08

### 变更

- **仓库更名为 `AI-Coding-Guide-Zh`**（原 `Claude-Code-x-OpenClaw-Guide-Zh`），更简洁、更包容
- 更新 README.md 所有仓库链接和徽章引用

### 新增

- **全库教程版本升级至 Claude Code v2.1.133**（从 v2.1.92/v2.1.119）
- **11 篇教程新增 v2.1.93→v2.1.133 功能内容**，覆盖以下重要变更：
  - effort 五级推理控制（新增 `xhigh`/`max`，Opus 4.7 推荐默认 `xhigh`）
  - MCP `alwaysLoad` 跳过 ToolSearch 懒加载
  - Hooks 接收 `effort.level` + `$CLAUDE_EFFORT` 环境变量
  - `PostToolUse` 可通过 `hookSpecificOutput.updatedToolOutput` 替换所有工具输出
  - `worktree.baseRef` 控制 worktree 分叉来源（`fresh`/`head`）
  - Monitor 后台监控工具
  - Session Recap（终端失焦后自动回顾）
  - 自定义主题（Plugin 声明 color palette）
  - `skillOverrides` 设置控制 Skill 可见性
  - `/skills` 搜索过滤、`/proactive` 命令（`/loop` 别名）
  - `parentSettingsBehavior` 企业托管设置合并策略
  - `claude project purge` 清理项目状态
  - `CLAUDE_CODE_SESSION_ID` / `CLAUDE_CODE_FORK_SUBAGENT` 环境变量
  - `--channels` 支持 Console auth 登录
  - Native binaries（CLI 迁移到原生二进制文件）
  - Opus 4.7 成为 Max/Team Premium 默认模型
- **新增 Codex 教程目录** `docs/codex/`，含 CX-11 对比指南

### 修改

- 01-安装指南：effort 说明更新为五级，模型名更新为 Opus 4.7
- 02-基础使用：新增 Monitor/Session Recap/自定义主题/Native binaries 章节
- 03-Commands：新增 v2.1.93→v2.1.133 命令增强，effort 五级对比
- 04-MCP：新增 `alwaysLoad` 配置说明，更新变更摘录至 v2.1.133
- 05-Hooks：更新变更摘录至 v2.1.133，修正 PostToolUse 输出机制说明
- 06-Subagent：新增 `worktree.baseRef`、`CLAUDE_CODE_FORK_SUBAGENT` 说明
- 07-Skills：新增 `skillOverrides` 和 `/skills` 搜索过滤
- 08-Plugins：新增自定义主题、`plugin prune`、`--plugin-url`、`.zip` 支持
- 09-SDK：新增 `CLAUDE_CODE_SESSION_ID`、`localSettings`、`mcp_authenticate`
- 11-企业实战：新增 `parentSettingsBehavior`、`claude project purge`、Bedrock service tier
- 12-Remote-Control：扩展 v2.1.121+ 和 v2.1.133 增强条目
- 13-Channels：更新 `--channels` 支持 Console auth
- README：版本徽章、功能描述、版本追踪表同步更新

---

## [v2.1.119] - 2026-04-26

### 新增

- **全库教程版本升级至 Claude Code v2.1.119 + OpenClaw v2026.4.24**
- 版本号、徽章、README 同步更新

---

## [v2.1.92] - 2026-04-29

### 修复

- 补齐 12/13 教程课程信息头（GitHub / 公众号 / X / 博客）
- 同步 Claude Code command 和 agent 文档
- 移除误提交的计划文件（findings.md、progress.md）

---

## [v2.1.78] - 2026-04-07

### 新增

- **新增 12-Remote Control 完整指南**
- **新增 13-Channels 与计划任务完整指南**
- 01-安装指南新增第 8.5 部分：模型配置完整章节（+379 行）
- 删除独立的 12-Claude-Code 模型配置完整指南.md（内容合并到安装指南）
- 同步更新快速导航卡：14 个教程 → 13 个，序号重排
- 对齐 Remote/Channels 教程序号与文件名（12/13）并修正全库链接

### 修复

- 教程版本对齐与口径修正
- 在输出格式代码块前添加空行，改进文档渲染

### 修改

- 更新配置文档：`allowedTools` 改为 `allow`

---

## [v2.1.69] - 2026-03-18

### 新增

- 补充 Claude Code v2.1.69→v2.1.78 新功能到全部教程
- 更新 README 和快速导航卡，同步 v2.1.78 新功能

---

## OpenClaw 重写 - 2026-02-26

### 新增

- OpenClaw 全套文档重写 — 技术错误修复 + 小白友好化改造
- 新增 06-Subagent 子代理教程 + 全站模型名更新

### 修复

- 全量审核修复 OpenClaw 教程 01-11 全部虚构配置
- 修正 OpenClaw 教程 10 处配置错误（验证 GitHub 源码）
- 修复 6 篇 OpenClaw 教程 28 处虚构配置 + GitHub 仓库名修正
- 安装指南新增 NPM 备选方案 + Windows PATH 详细排查
- 修正安装指南 Windows 路径 + Hooks 输出机制纠错

---

## 项目初始化 - 2026-02-25

### 新增

- **双顶流合璧 — Claude Code x OpenClaw 中文教程** 发布
- 初始化 Claude Code 中文教程项目
- 新增 Git Worktree 内置并行任务隔离功能文档
- 全面迭代教程适配 Claude Code v2.1.45
- 清除全部待办项 — Task/Subagent、权限系统、新 MCP 服务

### 修复

- 重写 07-Plugins 教程 — 清除 60+ 处虚假 CLI 命令
- 全面体检修复 — 7 个 CRITICAL + 5 个 WARNING
- 二次验证修复 5 篇文档 16 处确认错误
- 修复 03-Commands 指南嵌套代码块渲染问题
- 移除 02-基础使用指南中 `#` 前缀的错误描述

---

> 格式参考：基于 [Keep a Changelog](https://keepachangelog.com/zh-CN/)
