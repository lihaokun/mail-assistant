# mail-assistant

基于 MCP（Model Context Protocol）的本地 AI 邮件助手，可由 [Claude Code](https://claude.ai/code) 或 [Codex](https://developers.openai.com/codex) 按同一套规则整理 Thunderbird、Outlook 等客户端中的邮件。

## 功能

- checkpoint 断点续查：完整覆盖上次成功巡检至今的增量邮件
- 最近一周和一个月的重要邮件安全网
- 按 Message-ID 去重，并把系列邮件合并成时间线
- 用中文表格生成每日简报，提取行动项和截止日期
- 跨会话持久化待办事项与本地通讯录
- 检查近期日程和任务
- 发送、回复、转发和创建日程默认进入客户端复核界面，不自动执行

## 支持的代理

| 代理 | 项目说明文件 |
|---|---|
| Claude Code | `CLAUDE.md` |
| Codex | `AGENTS.md` |

两份说明文件必须保持一致，仓库中的 GitHub Actions 会自动检查。

## 支持的邮件客户端

| 客户端 | 状态 | 平台 | MCP 插件 |
|---|---|---|---|
| Thunderbird | ✅ 可用 | Windows、Linux、macOS | [`thunderbird-mcp`](https://github.com/TKasperczyk/thunderbird-mcp) |
| Outlook | ✅ 可用 | Windows | [`outlook-mcp-server`](https://pypi.org/project/outlook-mcp-server/) |

## 快速开始

```bash
git clone https://github.com/lihaokun/mail-assistant.git
cd mail-assistant
```

然后在该目录中启动你使用的代理：

```bash
claude
# 或
codex
```

首次启动时，代理会检测本地是否配置邮件 MCP，并按照 `plugins/` 中的说明引导安装。MCP 的真实路径和本机配置只保存在本地，不应提交到仓库。

## 工作原理

1. 若尚未配置邮件 MCP，进入安装模式。
2. 若 `briefings/TODO.md` 不存在，从安全模板初始化本地待办。
3. 读取上次巡检 checkpoint；首次运行默认回看过去 7 天。
4. 完整扫描增量窗口，同时执行周/月重要邮件安全网和日程检查。
5. 将简报写入 `briefings/YYYY-MM-DD.md`；确认落盘后才推进 checkpoint。

## 隐私与安全

- `briefings/`、`contacts.md`、真实 `.mcp.json`、附件、邮件导出和本机代理配置均被 Git 忽略。
- 仓库只提供 `contacts.example.md` 和 `templates/TODO.md`，其中不包含真实个人数据。
- 疑似密码、API key 或访问令牌只报告风险，不复述原值。
- 压缩包、可执行文件和脚本等高风险附件不会自动下载或打开。
- 对外汇报遵循数据最小化原则，不展示不必要的完整账号、订单、地址或邮件原文。

各客户端安装说明见 [`plugins/`](plugins/)，完整助手行为分别定义在 [`CLAUDE.md`](CLAUDE.md) 和 [`AGENTS.md`](AGENTS.md)。
