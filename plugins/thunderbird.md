# Thunderbird MCP 插件

本文按 `thunderbird-mcp` v0.7.4 的能力整理。

## 依赖

- [Thunderbird](https://www.thunderbird.net/) 已安装并配置好邮件账户
- Node.js >= 18
- Thunderbird 在使用 MCP 时保持运行

## 安装步骤

### 1. 获取扩展和 bridge

```bash
git clone https://github.com/TKasperczyk/thunderbird-mcp.git
cd thunderbird-mcp
```

仓库已包含预构建的 `dist/thunderbird-mcp.xpi`，普通用户不需要自行构建。

### 2. 安装 XPI

在 Thunderbird 中打开“工具 → 附加组件和主题”，从齿轮菜单选择“从文件安装附加组件”，选择：

```text
thunderbird-mcp/dist/thunderbird-mcp.xpi
```

安装后重启 Thunderbird。v0.7.3 及之后的版本支持通过 Thunderbird 的附加组件更新检查自动更新；更新通常在下次重启时生效。

### 3. 配置 MCP 客户端

Claude Code 项目配置示例：

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

Codex 项目配置示例：

```toml
[mcp_servers.thunderbird-mail]
command = "node"
args = ["<thunderbird-mcp 绝对路径>/mcp-bridge.cjs"]
```

把占位符替换为本机绝对路径，但不要把含真实路径的配置提交到 Git。

### 4. 重启代理

Thunderbird 和扩展启动后，重新连接或重启 Claude Code/Codex，使客户端重新读取工具列表。

## 连接机制

- 扩展在本机 `127.0.0.1` 上监听动态端口 `8765–8774`。
- 启动时生成带会话令牌的 `connection.json`；bridge 会自动发现端口和令牌。
- 可用 `THUNDERBIRD_MCP_CONNECTION_FILE` 显式指定非标准位置。
- Linux Snap、Flatpak、Betterbird Flatpak 和 macOS 沙箱路径由 bridge 自动探测。

### Windows v0.7.4 兼容说明

如果 Windows 上因为临时目录已存在而无法启动 MCP，可暂时使用 fork 中的 [`codex/v0.7.4-windows-hotfix`](https://github.com/lihaokun/thunderbird-mcp/tree/codex/v0.7.4-windows-hotfix) 分支；该修复只调整临时目录创建的兼容行为，不应与 mail-assistant 的规则文件混合提交。

## 主要能力

| 类别 | 工具与能力 |
|---|---|
| 邮件读取 | `searchMessages`、`getRecentMessages`、`getMessage`、`getMessages`、`displayMessage` |
| 邮件管理 | 标记、标签、移动、删除，创建/重命名/移动文件夹，清空垃圾箱或垃圾邮件 |
| 撰写 | `sendMail`、`replyToMessage`、`forwardMessage`，默认打开复核窗口 |
| 过滤器 | 列出、创建、更新、删除、排序和应用 Thunderbird 过滤器 |
| 通讯录 | 搜索、读取、创建、更新和删除联系人 |
| 日历与任务 | 列出/创建/更新/删除日程，列出/创建/更新任务 |
| 访问控制 | 查看允许访问的账号；账号和工具权限只能在扩展设置页修改 |

`searchMessages` 默认可按 RFC Message-ID 去重；`getMessages` 可批量读取邮件，巡检时仍须分页覆盖全部搜索结果。

## 安全说明

- HTTP 服务默认只绑定 localhost，并要求会话级 bearer token。
- 扩展默认阻止 `skipReview`，发送邮件、回复、转发、创建日程和任务都需要用户复核。
- 只有用户在扩展设置中明确关闭该安全限制后，`skipReview: true` 才会生效。
- 可在扩展设置中限制 MCP 可见的邮箱账号和工具。
- 不要在不可信网络上启用“监听所有网络接口”。
- mail-assistant 的巡检规则不会自动发送、回复、转发，也不会自动打开高风险附件。

## 常见问题

| 问题 | 处理方式 |
|---|---|
| 连接被拒绝 | 确认 Thunderbird 正在运行且扩展已启用 |
| 找不到 `connection.json` | 配置 `THUNDERBIRD_MCP_CONNECTION_FILE` 指向实际文件 |
| 更新后缺少工具 | 重连 MCP 或重启代理以刷新 `tools/list` |
| 最近邮件缺失 | 在 Thunderbird 中同步/修复对应 IMAP 文件夹 |
| 正文搜索无结果 | 为 IMAP 账号启用离线同步，供 Gloda 建立索引 |
