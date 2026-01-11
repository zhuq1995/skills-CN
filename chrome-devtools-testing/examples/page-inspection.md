# 页面检查示例

## 目标
检查页面结构，找到可交互元素，理解页面状态。

## 场景
用户想要了解一个本地 Web 应用的页面结构，找出所有可交互的元素（按钮、链接、输入框等）。

## 步骤

### 1. 导航到目标页面

```
mcp__chrome-devtools-mcp__navigate_page
{
  "url": "http://localhost:5173"
}
```

### 2. 获取页面快照

```
mcp__chrome-devtools-mcp__take_snapshot
{}
```

**返回示例：**
```json
{
  "elements": [
    {"uid": "0", "role": "button", "name": "Submit"},
    {"uid": "1", "role": "textbox", "name": "Email"},
    {"uid": "2", "role": "link", "name": "Home"}
  ]
}
```

### 3. 从快照中识别目标元素

分析快照返回的元素列表，找到：
- 按钮：`role: "button"`
- 输入框：`role: "textbox"`, `"searchbox"`, `"combobox"`
- 链接：`role: "link"`
- 其他可交互元素

### 4. 执行操作

使用获取到的 uid 与元素交互：

```
mcp__chrome-devtools-mcp__click
{
  "uid": "0"
}
```

```
mcp__chrome-devtools-mcp__fill
{
  "uid": "1",
  "value": "user@example.com"
}
```

### 5. 验证结果

```
mcp__chrome-devtools-mcp__take_screenshot
{}
```

## 完整工作流示例

**用户请求：** "帮我看看这个页面上有什么按钮"

1. `navigate_page` - 打开页面
2. `take_snapshot` - 获取所有元素
3. 从快照中筛选 `role: "button"` 的元素
4. 报告给用户："找到 3 个按钮：Submit (uid:0), Cancel (uid:5), Help (uid:8)"

## 提示

- 动态应用需要在每次操作后重新 `take_snapshot` 获取最新状态
- 使用 `verbose: true` 获取更详细的元素信息
- 元素的 `name` 属性通常显示可见文本
