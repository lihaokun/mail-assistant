# Outlook MCP Server (win32com) — 功能规格

> 基于 thunderbird-mcp 的功能集，适配 Windows Outlook COM 接口

## 概述

通过 Python `win32com.client` 操控本地 Outlook 桌面客户端，提供 MCP (Model Context Protocol) 工具接口。所有操作在本地完成，不依赖云端 API 或 OAuth 认证。

## 技术栈

- **语言**：Python 3.8+
- **COM 接口**：`pywin32` (`win32com.client`)
- **MCP 框架**：`mcp` (Python MCP SDK)
- **传输**：stdio（标准输入输出）
- **平台**：Windows（需 Outlook 桌面版运行中）

## 工具列表

### 1. 账户与文件夹

#### `listAccounts`
列出 Outlook 中配置的所有邮箱账户及其信息。

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| — | — | — | 无参数 |

**返回**：账户列表（名称、邮箱地址、账户类型）

#### `listFolders`
列出所有邮件文件夹及消息数量。

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| accountId | string | 否 | 限定某个账户 |

**返回**：文件夹树（名称、路径、邮件总数、未读数）

#### `createFolder`
在指定父文件夹下创建子文件夹。

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| parentFolderPath | string | 是 | 父文件夹路径 |
| name | string | 是 | 新文件夹名称 |

### 2. 邮件搜索与读取

#### `searchMessages`
搜索邮件，支持按关键词、日期范围过滤。

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| query | string | 是 | 搜索关键词（匹配主题、发件人、收件人） |
| startDate | string | 否 | 起始日期 (ISO 8601) |
| endDate | string | 否 | 截止日期 (ISO 8601) |
| maxResults | number | 否 | 最大返回数（默认 50，上限 200） |
| sortOrder | string | 否 | 排序：`desc`（默认，最新优先）/ `asc` |

**返回**：邮件摘要列表（messageId, folderPath, subject, sender, date, read/unread）

#### `getRecentMessages`
获取最近邮件，支持文件夹、天数、未读过滤。

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| folderPath | string | 否 | 文件夹路径（默认所有收件箱） |
| daysBack | number | 否 | 最近 N 天（默认 7） |
| maxResults | number | 否 | 最大返回数（默认 50，上限 200） |
| unreadOnly | boolean | 否 | 仅未读（默认 false） |

**返回**：邮件摘要列表

#### `getMessage`
读取邮件完整内容。

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| messageId | string | 是 | 邮件 ID |
| folderPath | string | 是 | 文件夹路径 |
| saveAttachments | boolean | 否 | 是否保存附件到本地临时目录（默认 false） |

**返回**：完整邮件（subject, sender, to, cc, date, body, attachments）

### 3. 邮件操作

#### `sendMail`
创建并发送新邮件（触发 Outlook 安全确认）。

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| to | string | 是 | 收件人 |
| subject | string | 是 | 主题 |
| body | string | 是 | 正文 |
| cc | string | 否 | 抄送 |
| bcc | string | 否 | 密送 |
| isHtml | boolean | 否 | 正文是否为 HTML（默认 false） |
| from | string | 否 | 发件账户 |
| attachments | string[] | 否 | 附件文件路径 |

#### `replyToMessage`
回复指定邮件，保持邮件线程。

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| messageId | string | 是 | 原邮件 ID |
| folderPath | string | 是 | 文件夹路径 |
| body | string | 是 | 回复正文 |
| replyAll | boolean | 否 | 是否回复所有人（默认 false） |
| isHtml | boolean | 否 | 正文是否为 HTML |
| to | string | 否 | 覆盖收件人 |
| cc | string | 否 | 抄送 |
| bcc | string | 否 | 密送 |
| from | string | 否 | 发件账户 |
| attachments | string[] | 否 | 附件 |

#### `forwardMessage`
转发邮件，保留原始附件。

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| messageId | string | 是 | 原邮件 ID |
| folderPath | string | 是 | 文件夹路径 |
| to | string | 是 | 转发目标 |
| body | string | 否 | 附加正文 |
| isHtml | boolean | 否 | 正文是否为 HTML |
| cc | string | 否 | 抄送 |
| bcc | string | 否 | 密送 |
| from | string | 否 | 发件账户 |
| attachments | string[] | 否 | 额外附件 |

#### `updateMessage`
更新邮件状态：已读/未读、标星、移动文件夹、移入回收站。

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| messageId | string | 是 | 邮件 ID |
| folderPath | string | 是 | 当前文件夹路径 |
| read | boolean | 否 | 设为已读/未读 |
| flagged | boolean | 否 | 设为标星/取消标星 |
| moveTo | string | 否 | 目标文件夹（不可与 trash 同用） |
| trash | boolean | 否 | 移入回收站（不可与 moveTo 同用） |

#### `deleteMessages`
批量删除邮件。

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| messageIds | string[] | 是 | 邮件 ID 列表 |
| folderPath | string | 是 | 文件夹路径 |

### 4. 联系人

#### `searchContacts`
搜索联系人。

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| query | string | 是 | 搜索关键词（姓名或邮箱） |

**返回**：联系人列表（姓名、邮箱、电话等）

### 5. 日历

#### `listCalendars`
列出所有日历。

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| — | — | — | 无参数 |

**返回**：日历列表（名称、ID、是否可写）

#### `createEvent`
创建日历事件（触发 Outlook 确认对话框）。

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| title | string | 是 | 事件标题 |
| startDate | string | 是 | 开始时间 (ISO 8601) |
| endDate | string | 否 | 结束时间（默认 +1 小时） |
| location | string | 否 | 地点 |
| description | string | 否 | 描述 |
| calendarId | string | 否 | 目标日历 ID |
| allDay | boolean | 否 | 全天事件（默认 false） |

## win32com 关键映射

| MCP 工具 | Outlook COM 对象/方法 |
|----------|----------------------|
| listAccounts | `Namespace.Accounts` |
| listFolders | `Namespace.Folders` 递归遍历 |
| searchMessages | `Items.Restrict()` / `AdvancedSearch()` |
| getRecentMessages | `Items.Restrict("[ReceivedTime] >= ...")` |
| getMessage | `Items.Item(id)` → `.Subject`, `.Body`, `.HTMLBody` 等 |
| sendMail | `CreateItem(0)` → `.Send()` |
| replyToMessage | `MailItem.Reply()` / `.ReplyAll()` |
| forwardMessage | `MailItem.Forward()` |
| updateMessage | `.UnRead`, `.FlagStatus`, `.Move()`, `.Delete()` |
| deleteMessages | `.Delete()` 批量 |
| createFolder | `Folder.Folders.Add()` |
| searchContacts | `GetDefaultFolder(10).Items.Restrict()` |
| listCalendars | `Namespace.Folders` → 日历类型文件夹 |
| createEvent | `CreateItem(1)` → `.Save()` / `.Display()` |

## Outlook 文件夹常量 (OlDefaultFolders)

| 常量 | 值 | 说明 |
|------|----|------|
| olFolderInbox | 6 | 收件箱 |
| olFolderOutbox | 4 | 发件箱 |
| olFolderSentMail | 5 | 已发送 |
| olFolderDeletedItems | 3 | 已删除 |
| olFolderDrafts | 16 | 草稿 |
| olFolderCalendar | 9 | 日历 |
| olFolderContacts | 10 | 联系人 |
| olFolderJunk | 23 | 垃圾邮件 |

## 注意事项

1. **安全提示**：Outlook Object Model Guard 会在发送邮件时弹出确认，这是预期行为
2. **Outlook 必须运行**：COM 接口需要 Outlook 进程在前台或后台运行
3. **编码**：注意中文邮件的编码处理
4. **邮件 ID**：Outlook COM 中使用 `EntryID` 作为邮件唯一标识
5. **性能**：大量邮件搜索时优先使用 `Items.Restrict()` 而非遍历，`AdvancedSearch()` 适合全文搜索
6. **线程安全**：COM 对象不可跨线程使用，需在同一线程操作
