# Outlook MCP 插件

## 依赖

- Windows + [Outlook](https://www.microsoft.com/outlook) 桌面版已安装并配置好邮件账户
- Python >= 3.10
- Outlook 必须保持运行

## 安装步骤

以下步骤由 Claude 自动执行：

### 1. 安装 outlook-mcp-server

```bash
pip install outlook-mcp-server
```

### 2. 验证安装

```bash
outlook-mcp-server --version
```

### 3. 生成 .mcp.json

在 mail-assistant 目录下生成：

```json
{
  "mcpServers": {
    "outlook": {
      "command": "outlook-mcp-server"
    }
  }
}
```

### 4. 重启 Claude Code

安装完成后需重启 Claude Code 以加载 MCP 服务。

## 可用工具

| 工具 | 说明 |
|------|------|
| `listAccounts` | 列出邮箱账户 |
| `listFolders` | 列出文件夹 |
| `createFolder` | 创建子文件夹 |
| `searchMessages` | 搜索邮件 |
| `getRecentMessages` | 获取最近邮件 |
| `getMessage` | 读取邮件完整内容（含附件） |
| `sendMail` | 撰写新邮件（支持附件） |
| `replyToMessage` | 回复邮件 |
| `forwardMessage` | 转发邮件 |
| `updateMessage` | 标记已读/标星/移动/移入回收站 |
| `deleteMessages` | 删除邮件 |
| `searchContacts` | 搜索联系人 |
| `listCalendars` | 列出日历 |
| `createEvent` | 创建日历事件 |

## 注意事项

- Outlook 桌面客户端必须保持运行，否则 COM 接口会报错
- 发送/回复/转发/创建事件会打开 Outlook 窗口供用户确认，不会自动发送
- searchMessages 结果可能有重复，注意按 messageId 去重
