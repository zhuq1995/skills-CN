---
name: chrome-devtools-testing
description: 使用 Chrome DevTools MCP 协议测试本地 Web 应用的工具包。支持实时调试、页面交互、表单测试、网络请求监控、控制台日志查看和截图。适用于：(1) 调试运行中的 Web 应用 (2) 验证前端功能 (3) 检查网络请求和响应 (4) 查看控制台错误 (5) 测试表单交互 (6) 性能分析
---

# Chrome DevTools Web 应用测试

使用 chrome-devtools-mcp 工具与浏览器直接交互，测试本地 Web 应用。

**可用的参考示例**：
- `examples/page-inspection.md` - 页面检查与元素发现
- `examples/form-testing.md` - 表单填写与提交测试
- `examples/network-debugging.md` - 网络请求与 API 调试

## 快速开始

**核心工作流**：
1. 导航到页面 → `navigate_page`
2. 获取快照 → `take_snapshot` (获取元素 uid)
3. 执行操作 → `click`, `fill`, `hover` 等
4. 检查结果 → `take_screenshot`, `list_console_messages`

## 决策树：选择方案

```
用户任务 → 静态 HTML 文件？
    ├─ 是 → 用 file:// URL 导航
    │         然后 take_snapshot 获取元素
    │
    └─ 否（动态应用）→ 应用是否运行？
        ├─ 否 → 提示用户启动应用，等待其就绪后再继续
        │
        └─ 是 → navigate_page 到 localhost
                → take_snapshot 获取状态
                → 使用 uid 执行操作
```

## 常用工具

### 导航与页面管理
- `mcp__chrome-devtools-mcp__list_pages` - 列出所有打开的页面
- `mcp__chrome-devtools-mcp__select_page` - 选择要操作的页面
- `mcp__chrome-devtools-mcp__navigate_page` - 导航到 URL
- `mcp__chrome-devtools-mcp__new_page` - 创建新页面

### 页面检查
- `mcp__chrome-devtools-mcp__take_snapshot` - **关键**：获取页面快照和元素 uid
- `mcp__chrome-devtools-mcp__take_screenshot` - 截取屏幕截图
- `mcp__chrome-devtools-mcp__evaluate_script` - 执行 JavaScript

### 元素交互
- `mcp__chrome-devtools-mcp__click` - 点击元素 (需要 uid)
- `mcp__chrome-devtools-mcp__fill` - 填写输入 (需要 uid)
- `mcp__chrome-devtools-mcp__hover` - 悬停元素 (需要 uid)
- `mcp__chrome-devtools-mcp__drag` - 拖拽元素

### 表单操作
- `mcp__chrome-devtools-mcp__fill_form` - 批量填写表单
- `mcp__chrome-devtools-mcp__upload_file` - 上传文件
- `mcp__chrome-devtools-mcp__select` - 选择下拉选项

### 调试工具
- `mcp__chrome-devtools-mcp__list_console_messages` - 查看控制台日志
- `mcp__chrome-devtools-mcp__list_network_requests` - 查看网络请求
- `mcp__chrome-devtools-mcp__get_console_message` - 获取特定日志详情
- `mcp__chrome-devtools-mcp__get_network_request` - 获取特定请求详情

### 键盘与特殊操作
- `mcp__chrome-devtools-mcp__press_key` - 模拟键盘按键 (Enter, Tab, Ctrl+C 等)
- `mcp__chrome-devtools-mcp__handle_dialog` - 处理 alert/confirm/prompt 对话框
- `mcp__chrome-devtools-mcp__resize_page` - 调整浏览器视口大小
- `mcp__chrome-devtools-mcp__emulate` - 模拟设备/网络条件/地理位置

### iframe 操作
- `mcp__chrome-devtools-mcp__iframe_click` - 点击 iframe 内元素
- `mcp__chrome-devtools-mcp__iframe_fill` - 填写 iframe 内输入框

### 性能分析
- `mcp__chrome-devtools-mcp__performance_start_trace` - 开始性能追踪
- `mcp__chrome-devtools-mcp__performance_stop_trace` - 停止追踪并获取报告
- `mcp__chrome-devtools-mcp__performance_analyze_insight` - 分析性能洞察

### 等待与同步
- `mcp__chrome-devtools-mcp__wait_for` - 等待文本出现

## 工作流示例

### 1. 页面侦察模式

**先检查后执行** - 所有交互的通用模式：

```
第一步：获取快照
→ take_snapshot

第二步：从快照中找到目标元素的 uid

第三步：执行操作
→ click(uid="...") 或 fill(uid="...", value="...")
```

### 2. 表单测试工作流

```
1. take_snapshot - 获取表单元素 uid
2. fill_form - 批量填写
3. click - 提交按钮
4. take_screenshot - 验证结果
5. list_console_messages - 检查错误
```

### 3. 网络调试工作流

```
1. navigate_page - 导航到目标页面
2. 执行触发网络请求的操作
3. list_network_requests - 查看所有请求
4. get_network_request - 检查特定请求的响应
```

### 4. 响应式设计测试

```
1. resize_page - 调整视口大小 (如移动设备尺寸)
2. take_screenshot - 验证布局
3. 重复测试不同断点
```

## 最佳实践

✅ **务必先 take_snapshot** - 获取元素 uid 是所有交互的前提
✅ **使用 wait_for** - 等待动态内容加载完成
✅ **检查控制台** - 操作后检查 `list_console_messages` 查找错误
✅ **验证网络请求** - 使用 `list_network_requests` 检查 API 调用
✅ **截图验证** - 关键操作后使用 `take_screenshot` 记录状态

❌ **不要**在没有 uid 的情况下尝试交互元素
❌ **不要**忘记在操作后验证结果
❌ **不要**假设页面已完全加载 - 动态内容可能需要等待

## 处理大型页面

当页面元素过多导致 `take_snapshot` 返回内容超长、上下文超出限制时，使用以下策略：

### 策略 1：仅获取可交互元素

```
mcp__chrome-devtools-mcp__take_snapshot
{
  "interactable": true
}
```

这会过滤掉纯展示性元素，只返回按钮、输入框、链接等可交互元素。

### 策略 2：使用 JavaScript 定向查找

跳过 `take_snapshot`，直接用 `evaluate_script` 查询特定元素：

```
mcp__chrome-devtools-mcp__evaluate_script
{
  "script": "Array.from(document.querySelectorAll('button, input, a')).map(el => ({tag: el.tagName, id: el.id, text: el.textContent.trim().slice(0,30)}))"
}
```

更精确的定向查找：

```
mcp__chrome-devtools-mcp__evaluate_script
{
  "script": "document.querySelector('#login-form button[type=submit]')?.id"
}
```

### 策略 3：分区域检查

对于复杂页面，分区域获取元素而非全量快照：

```
第一步：确定目标区域
→ evaluate_script 查找特定容器

第二步：区域内快照（如支持）
→ 限定在特定 DOM 子树

第三步：执行操作
→ 使用获取的信息操作元素
```

### 策略 4：轻量级验证工作流

当不需要完整快照时，使用替代方案：

```
1. evaluate_script - 检查特定元素是否存在
2. click/fill - 直接用选择器操作（如 MCP 支持）
3. take_screenshot - 视觉验证结果
4. list_console_messages - 检查错误
```

### 策略 5：控制返回信息量

- ❌ 避免使用 `verbose: true`（除非必要）
- ✅ 只获取需要的元素属性
- ✅ 优先使用 `take_screenshot` 进行视觉验证

### 推荐的大型页面工作流

```
1. take_screenshot - 先截图了解页面布局
2. evaluate_script - 定向查询目标元素
3. 执行操作 - click/fill/hover
4. take_screenshot - 验证结果
5. list_console_messages - 检查错误
```

## 常见问题

**Q: 找不到元素？**
A: 重新运行 `take_snapshot` 获取最新状态。动态应用可能在初始快照后发生了变化。

**Q: 操作超时？**
A: 使用 `wait_for` 等待元素出现，或检查应用是否正确运行。

**Q: 需要测试响应式设计？**
A: 使用 `resize_page` 调整视口大小，或使用 `emulate` 模拟不同设备。

**Q: 如何调试 API 请求？**
A: 使用 `list_network_requests` 配合 `resourceTypes: ["xhr", "fetch"]` 过滤。

## 参考示例

详细的使用示例见 `examples/` 目录：

- `page-inspection.md` - 页面检查与元素发现
- `form-testing.md` - 表单填写与提交测试
- `network-debugging.md` - 网络请求与 API 调试
