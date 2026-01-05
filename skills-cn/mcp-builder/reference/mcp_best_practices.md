# MCP 服务器最佳实践

## 快速参考

### 服务器命名
- **Python**：`{service}_mcp`（如 `slack_mcp`）
- **Node/TypeScript**：`{service}-mcp-server`（如 `slack-mcp-server`）

### 工具命名
- 使用带服务前缀的 snake_case
- 格式：`{service}_{action}_{resource}`
- 示例：`slack_send_message`、`github_create_issue`

### 响应格式
- 同时支持 JSON 与 Markdown
- JSON 用于程序处理
- Markdown 面向人类阅读

### 分页
- 始终尊重 `limit` 参数
- 返回 `has_more`、`next_offset`、`total_count`
- 默认 20–50 项

### 传输
- **Streamable HTTP**：适合远程服务器、多客户端场景
- **stdio**：适合本地集成、命令行工具
- 避免 SSE（已被 streamable HTTP 取代）

---

## 服务器命名约定

遵循以下标准命名：

**Python**：`{service}_mcp`（全小写+下划线）
- 示例：`slack_mcp`、`github_mcp`、`jira_mcp`

**Node/TypeScript**：`{service}-mcp-server`（全小写+连字符）
- 示例：`slack-mcp-server`、`github-mcp-server`、`jira-mcp-server`

命名应通用、能描述集成服务、易从任务推断、且无版本号。

---

## 工具命名与设计

### 工具命名

1. 使用 snake_case：`search_users`、`create_project`、`get_channel_info`
2. 包含服务前缀：考虑你的 MCP 服务器可能与其他服务器并用
   - 用 `slack_send_message` 而不是 `send_message`
   - 用 `github_create_issue` 而不是 `create_issue`
3. 动作导向：以动词开头（get、list、search、create 等）
4. 具体：避免与其他服务器冲突的泛名

### 工具设计

- 工具描述必须聚焦且不含糊
- 描述需与实际功能精准一致
- 提供注解（readOnlyHint、destructiveHint、idempotentHint、openWorldHint）
- 操作保持聚焦与原子性

---

## 响应格式

返回数据的工具应支持多种格式：

### JSON（`response_format="json"`）
- 机器可读的结构化数据
- 包含所有可用字段与元数据
- 字段名与类型一致
- 用于程序化处理

### Markdown（`response_format="markdown"`，通常默认）
- 人类可读的格式化文本
- 使用标题、列表与格式提升清晰度
- 时间戳转换为人类可读形式
- 显示名后括号标注 ID
- 省略冗长元数据

---

## 分页

用于列资源的工具：

- 始终尊重 `limit`
- 实现分页：`offset` 或游标式
- 返回分页元数据：`has_more`、`next_offset`/`next_cursor`、`total_count`
- 切勿一次载入全部结果（大数据集尤需注意）
- 默认合理限制：20–50 项

示例响应：
```json
{
  "total": 150,
  "count": 20,
  "offset": 0,
  "items": [...],
  "has_more": true,
  "next_offset": 20
}
```

---

## 传输选项

### Streamable HTTP

**适用**：远程服务器、Web 服务、多客户端场景

**特征**：
- 通过 HTTP 的双向通信
- 支持多并发客户端
- 可作为 Web 服务部署
- 支持服务器向客户端通知

**使用于**：
- 同时服务多个客户端
- 云端部署
- 与 Web 应用集成

### stdio

**适用**：本地集成、命令行工具

**特征**：
- 标准输入/输出流通信
- 简单设置，无需网络配置
- 作为客户端子进程运行

**使用于**：
- 本地开发环境工具
- 桌面应用集成
- 单用户、单会话场景

**注意**：stdio 服务器不要向 stdout 打日志（用 stderr）。

### 选择传输

| 标准 | stdio | Streamable HTTP |
|------|-------|-----------------|
| 部署 | 本地 | 远程 |
| 客户端 | 单个 | 多个 |
| 复杂度 | 低 | 中 |
| 实时性 | 否 | 是 |

---

## 安全最佳实践

### 认证与授权

**OAuth 2.1**：
- 使用可信机构证书的安全 OAuth 2.1
- 在处理请求前验证访问令牌
- 仅接受针对你服务器的令牌

**API Keys**：
- 将密钥存储于环境变量，切勿写入代码
- 服务器启动时验证密钥
- 认证失败时提供清晰错误信息

### 输入校验

- 清理文件路径，防止目录遍历
- 验证 URL 与外部标识
- 检查参数大小与范围
- 防止系统调用中的命令注入
- 所有输入使用 schema 校验（Pydantic/Zod）

### 错误处理

- 不向客户端暴露内部错误
- 在服务器端记录安全相关错误
- 提供有用但不泄露细节的错误消息
- 错误后清理资源

### DNS 重绑定防护

本地运行的 streamable HTTP 服务器：
- 启用 DNS 重绑定防护
- 验证所有入站连接的 `Origin` 头
- 绑定到 `127.0.0.1` 而非 `0.0.0.0`

---

## 工具注解

为客户端理解工具行为提供注解：

| 注解 | 类型 | 默认 | 描述 |
|------|------|------|------|
| `readOnlyHint` | boolean | false | 工具不修改环境 |
| `destructiveHint` | boolean | true | 工具可能执行破坏性更新 |
| `idempotentHint` | boolean | false | 相同参数重复调用不会产生额外效果 |
| `openWorldHint` | boolean | true | 工具与外部实体交互 |

**重要**：注解是提示而非安全保证。客户端不应仅基于注解做安全关键决策。

---

## 错误处理

- 使用标准 JSON-RPC 错误码
- 在结果对象中报告工具错误（而非协议层错误）
- 提供具体可执行的错误消息与建议下一步
- 不暴露内部实现细节
- 错误后正确清理资源

示例：
```typescript
try {
  const result = performOperation();
  return { content: [{ type: "text", text: result }] };
} catch (error) {
  return {
    isError: true,
    content: [{
      type: "text",
      text: `Error: ${error.message}. Try using filter='active_only' to reduce results.`
    }]
  };
}
```

---

## 测试要求

全面测试应覆盖：

- 功能测试：验证在有效/无效输入下的正确执行
- 集成测试：测试与外部系统的交互
- 安全测试：验证认证、输入清理、限流
- 性能测试：检查负载与超时行为
- 错误处理：确保正确报告与清理

---

## 文档要求

- 提供清晰的工具与能力文档
- 为每个重要特性提供至少 3 个可用示例
- 记录安全考量
- 指定所需权限与访问级别
- 记录速率限制与性能特征
