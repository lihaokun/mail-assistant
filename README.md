# mail-assistant

基于 [Claude Code](https://claude.ai/code) 的 AI 邮件助手，通过 MCP（Model Context Protocol）支持多种邮件客户端。

## 功能

- 每次启动自动生成当日邮件简报
- 智能过滤：标注学术、工作、需要行动的重要邮件
- 跨会话持久化待办事项
- 直接在 Claude 中发送、回复、转发邮件
- 日历事件管理

## 依赖

- [Claude Code](https://claude.ai/code)
- 支持的邮件客户端（见下表）
- Node.js >= 18

## 支持的邮件客户端

| 客户端 | 状态 | 平台 |
|--------|------|------|
| Thunderbird | ✅ 可用 | Linux、macOS |
| Outlook | 🔜 即将支持 | Windows、macOS |

## 快速开始

```bash
git clone https://github.com/lihaokun/mail-assistant.git
cd mail-assistant
claude
```

首次启动时，Claude 会自动引导完成安装配置。

## 工作原理

首次启动时，Claude 检测到未配置 MCP，会引导你选择并安装对应的邮件客户端插件。安装完成后，每次启动自动进入邮件助手模式，汇报当日邮件。

助手行为定义在 `CLAUDE.md` 中，各客户端的安装说明在 `plugins/` 目录下。
