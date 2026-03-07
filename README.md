# mail-assistant

An AI-powered mail assistant that runs inside [Claude Code](https://claude.ai/code). Supports multiple mail clients via MCP (Model Context Protocol).

## Features

- Daily email briefing on startup
- Smart filtering: academic, work, and action-required emails highlighted
- Persistent TODO tracking across sessions
- Send, reply, forward emails directly from Claude
- Calendar event management

## Requirements

- [Claude Code](https://claude.ai/code)
- A supported mail client (see below)
- Node.js >= 18

## Supported Mail Clients

| Client | Status | Platform |
|--------|--------|----------|
| Thunderbird | ✅ Available | Linux, macOS |
| Outlook | 🔜 Coming soon | Windows, macOS |

## Getting Started

```bash
git clone https://github.com/lihaokun/mail-assistant.git
cd mail-assistant
claude
```

Claude will guide you through the setup on first launch.

## How It Works

On first launch, Claude detects that no MCP is configured and walks you through installing your preferred mail client plugin. Once installed, every subsequent launch automatically delivers a daily briefing of your emails.

The assistant behavior is defined in `CLAUDE.md` and plugin installation instructions live in `plugins/`.
