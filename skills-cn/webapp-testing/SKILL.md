---
name: webapp-testing
description: 使用 Playwright 与测试本地 Web 应用的工具包。支持验证前端功能、调试 UI 行为、截取浏览器截图，以及查看浏览器日志。
license: Complete terms in LICENSE.txt
---

# Web 应用测试

测试本地 Web 应用时，编写原生 Python Playwright 脚本。

**可用的辅助脚本**：
- `scripts/with_server.py` - Manages server lifecycle (supports multiple servers)

**务必先用 `--help` 运行脚本** 查看用法。除非先运行后发现确实需要定制化解决方案，否则不要阅读源码。这些脚本可能很大，会污染上下文窗口；它们的设计目标是作为黑盒直接调用，而不是被整段读入上下文。

## 决策树：选择方案

```
用户任务 → 是否为静态 HTML？
    ├─ 是 → 直接读取 HTML 文件以定位选择器
    │         ├─ 成功 → 用这些选择器编写 Playwright 脚本
    │         └─ 失败/不完整 → 按动态应用处理（见下）
    │
    └─ 否（动态 Web 应用）→ 服务是否已在运行？
        ├─ 否 → 运行：python scripts/with_server.py --help
        │        然后用辅助脚本托管服务 + 编写简化的 Playwright 脚本
        │
        └─ 是 → 先侦察后执行：
            1. 打开页面并等待 networkidle
            2. 截图或检查 DOM
            3. 从渲染后的页面状态中识别选择器
            4. 使用识别到的选择器执行动作
```

## 示例：使用 with_server.py

启动服务时，先运行 `--help`，再使用辅助脚本：

**单服务：**
```bash
python scripts/with_server.py --server "npm run dev" --port 5173 -- python your_automation.py
```

**多服务（例如后端 + 前端）：**
```bash
python scripts/with_server.py \
  --server "cd backend && python server.py" --port 3000 \
  --server "cd frontend && npm run dev" --port 5173 \
  -- python your_automation.py
```

编写自动化脚本时，只包含 Playwright 逻辑即可（服务生命周期会被自动托管）：
```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(headless=True) # Always launch chromium in headless mode
    page = browser.new_page()
    page.goto('http://localhost:5173') # Server already running and ready
    page.wait_for_load_state('networkidle') # CRITICAL: Wait for JS to execute
    # ... your automation logic
    browser.close()
```

## “先侦察后执行”模式

1. **检查渲染后的 DOM**：
   ```python
   page.screenshot(path='/tmp/inspect.png', full_page=True)
   content = page.content()
   page.locator('button').all()
   ```

2. 从检查结果中 **识别选择器**

3. 使用识别到的选择器 **执行动作**

## 常见坑点

❌ **Don't** inspect the DOM before waiting for `networkidle` on dynamic apps
✅ **Do** wait for `page.wait_for_load_state('networkidle')` before inspection

## 最佳实践

- **把内置脚本当作黑盒使用** - 为完成任务，先考虑 `scripts/` 目录下是否已有脚本可用。这些脚本可靠地封装了常见复杂工作流，不会污染上下文窗口。先用 `--help` 了解用法，再直接调用。
- Use `sync_playwright()` for synchronous scripts
- 用完务必关闭浏览器
- 使用可读性强的选择器：`text=`、`role=`、CSS 选择器或 ID
- 添加合适的等待：`page.wait_for_selector()` 或 `page.wait_for_timeout()`

## 参考文件

- **examples/** - 常见模式示例：
  - `element_discovery.py` - Discovering buttons, links, and inputs on a page
  - `static_html_automation.py` - Using file:// URLs for local HTML
  - `console_logging.py` - Capturing console logs during automation
