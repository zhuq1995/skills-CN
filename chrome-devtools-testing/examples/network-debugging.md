# 网络调试示例

## 目标
检查 Web 应用发出的网络请求，验证 API 调用是否正确，调试请求/响应问题。

## 场景
用户想要检查页面加载时的 API 请求，或者验证某个操作是否触发了正确的网络请求。

## 步骤

### 1. 导航到目标页面

```
mcp__chrome-devtools-mcp__navigate_page
{
  "url": "http://localhost:5173/dashboard"
}
```

### 2. 执行触发网络请求的操作

例如：点击按钮加载用户数据

```
mcp__chrome-devtools-mcp__take_snapshot
{}
```

```
mcp__chrome-devtools-mcp__click
{
  "uid": "load-data-button"
}
```

### 3. 列出网络请求

```
mcp__chrome-devtools-mcp__list_network_requests
{
  "resourceTypes": ["xhr", "fetch"]
}
```

**返回示例：**
```json
{
  "requests": [
    {"reqid": 12345, "url": "/api/users", "method": "GET", "status": 200},
    {"reqid": 12346, "url": "/api/stats", "method": "GET", "status": 200}
  ]
}
```

### 4. 获取特定请求详情

```
mcp__chrome-devtools-mcp__get_network_request
{
  "reqid": 12345
}
```

**返回详细信息：**
- Request URL
- Request Method
- Request Headers
- Request Body (POST/PUT)
- Response Status
- Response Headers
- Response Body

### 5. 分析请求

检查以下内容：
- ✅ 请求 URL 正确
- ✅ HTTP 方法正确（GET/POST/PUT/DELETE）
- ✅ 请求头包含认证信息
- ✅ 请求体格式正确（JSON）
- ✅ 响应状态码为 2xx
- ✅ 响应数据符合预期

## 常见网络调试场景

### 场景 1：API 调试

**问题：** 用户数据加载失败

1. 导航到页面
2. 触发数据加载
3. `list_network_requests` - 查看是否有失败的请求（status 4xx/5xx）
4. `get_network_request` - 检查请求详情，找出失败原因

### 场景 2：表单提交验证

**问题：** 表单提交后服务器无响应

1. 填写并提交表单
2. 检查 `list_network_requests` 是否有 POST 请求
3. 使用 `get_network_request` 查看：
   - 请求体是否包含表单数据
   - 请求头 Content-Type 是否正确
   - 响应状态码和错误信息

### 场景 3：认证问题

**问题：** API 返回 401/403

1. 执行需要认证的操作
2. 找到返回 401/403 的请求
3. 检查请求头中的 Authorization/Cookie
4. 验证 token 是否有效

### 场景 4：性能分析

**目标：** 找出加载慢的请求

1. 导航到页面
2. `list_network_requests` - 查看所有请求
3. 找出响应时间长的请求
4. 分析原因：数据量大、服务器慢、网络延迟

## 高级技巧

### 过滤请求类型

```
mcp__chrome-devtools-mcp__list_network_requests
{
  "resourceTypes": ["xhr"]
}
```

可用类型：`document`, `stylesheet`, `image`, `script`, `xhr`, `fetch`, `websocket`

### 分页查看请求

```
mcp__chrome-devtools-mcp__list_network_requests
{
  "pageSize": 50,
  "pageIdx": 0
}
```

### 保留导航历史

```
mcp__chrome-devtools-mcp__list_network_requests
{
  "includePreservedRequests": true
}
```

## 提示

- 如果请求太多，使用 `resourceTypes` 过滤
- 检查响应状态码：200（成功）、400（客户端错误）、500（服务器错误）
- CORS 问题通常会在控制台有额外错误信息
- 使用 `list_console_messages` 配合网络调试更有效
