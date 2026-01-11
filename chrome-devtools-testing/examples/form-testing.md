# 表单测试示例

## 目标
填写表单、提交数据，并验证结果是否正确。

## 场景
用户需要测试一个登录或注册表单，验证表单提交流程是否正常工作。

## 步骤

### 1. 获取页面快照

```
mcp__chrome-devtools-mcp__take_snapshot
{}
```

**快照返回表单元素：**
```json
{
  "elements": [
    {"uid": "0", "role": "textbox", "name": "Email"},
    {"uid": "1", "role": "textbox", "name": "Password", "hasPopup": "true"},
    {"uid": "2", "role": "button", "name": "Login"},
    {"uid": "3", "role": "checkbox", "name": "Remember me"}
  ]
}
```

### 2. 批量填写表单

```
mcp__chrome-devtools-mcp__fill_form
{
  "elements": [
    {"uid": "0", "value": "test@example.com"},
    {"uid": "1", "value": "password123"}
  ]
}
```

### 3. 点击复选框（可选）

```
mcp__chrome-devtools-mcp__click
{
  "uid": "3"
}
```

### 4. 提交表单

```
mcp__chrome-devtools-mcp__click
{
  "uid": "2"
}
```

### 5. 验证结果

#### 截图验证
```
mcp__chrome-devtools-mcp__take_screenshot
{
  "fullPage": true
}
```

#### 检查控制台错误
```
mcp__chrome-devtools-mcp__list_console_messages
{
  "types": ["error"]
}
```

#### 检查网络请求
```
mcp__chrome-devtools-mcp__list_network_requests
{
  "resourceTypes": ["xhr", "fetch"]
}
```

### 6. 等待响应（可选）

如果表单提交后页面会变化：

```
mcp__chrome-devtools-mcp__wait_for
{
  "text": "Welcome back"
}
```

## 常见表单测试场景

### 场景 1：登录失败验证
1. 填写错误凭据
2. 提交表单
3. 检查是否显示错误消息

### 场景 2：必填字段验证
1. 不填写必填字段
2. 尝试提交
3. 检查是否显示验证错误

### 场景 3：文件上传
```
mcp__chrome-devtools-mcp__upload_file
{
  "uid": "file-input-uid",
  "filePath": "/path/to/test/file.pdf"
}
```

## 提示

- 使用 `fill_form` 批量填写比多次 `fill` 更高效
- 密码字段通常 `hasPopup: true` 表示输入被隐藏
- 提交后检查网络请求可以验证数据是否正确发送
