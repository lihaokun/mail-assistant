# Thunderbird MCP 插件

## 依赖

- [Thunderbird](https://www.thunderbird.net/) 已安装并配置好邮件账户
- Node.js >= 18

## 安装步骤

以下步骤由 Claude 自动执行：

### 1. 克隆并构建扩展

```bash
git clone https://github.com/TKasperczyk/thunderbird-mcp.git
cd thunderbird-mcp
bash scripts/build.sh
```

### 2. 安装到 Thunderbird

```bash
bash scripts/install.sh
```

自动检测 Thunderbird profile 路径（Linux: `~/.thunderbird/*.default-release`，macOS: `~/Library/Thunderbird/Profiles/*.default-release`）。

### 3. 生成 .mcp.json

在 mail-assistant 目录下生成（路径自适应当前机器）：

```json
{
  "mcpServers": {
    "thunderbird-mail": {
      "type": "stdio",
      "command": "node",
      "args": ["<thunderbird-mcp 绝对路径>/mcp-bridge.cjs"]
    }
  }
}
```

### 4. 重启 Thunderbird

安装扩展后需重启 Thunderbird 使扩展生效，然后重启 Claude Code。

## 可用工具

| 工具 | 说明 |
|------|------|
| `listAccounts` | 列出邮箱账户 |
| `listFolders` | 列出文件夹 |
| `searchMessages` | 搜索邮件 |
| `getRecentMessages` | 获取最近邮件 |
| `getMessage` | 读取邮件完整内容（含附件） |
| `sendMail` | 撰写新邮件 |
| `replyToMessage` | 回复邮件 |
| `forwardMessage` | 转发邮件 |
| `updateMessage` | 标记已读/标星/移动/移入回收站 |
| `deleteMessages` | 删除邮件 |
| `searchContacts` | 搜索联系人 |
| `listCalendars` | 列出日历 |
| `createEvent` | 创建日历事件 |

## 注意事项

- Thunderbird 必须保持运行，MCP bridge 通过 `localhost:8765` 与扩展通信
- 发送/回复/转发会打开 Thunderbird 撰写窗口供用户确认，不会自动发送
