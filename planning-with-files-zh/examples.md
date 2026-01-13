# 示例：文件规划的实际应用

## 示例1：研究任务

**用户请求：** "研究早晨锻炼的好处并写一篇总结"

### 循环1：创建计划
```bash
Write .claude_tasks/task_morning_exercise/task_plan.md
```

```markdown
# 任务计划：早晨锻炼益处研究

## 目标
创建关于早晨锻炼益处的研究总结。

## 阶段
- [ ] 阶段1：创建此计划 ✓
- [ ] 阶段2：搜索和收集来源
- [ ] 阶段3：综合发现
- [ ] 阶段4：交付总结

## 关键问题
1. 身体健康益处有哪些？
2. 心理健康益处有哪些？
3. 有哪些科学研究支持这一点？

## 状态
**当前处于阶段1** - 创建计划
```

### 循环2：研究
```bash
Read .claude_tasks/task_morning_exercise/task_plan.md  # 刷新目标
WebSearch "早晨锻炼益处"
Write .claude_tasks/task_morning_exercise/notes.md     # 存储发现
Edit .claude_tasks/task_morning_exercise/task_plan.md  # 标记阶段2完成
```

### 循环3：综合
```bash
Read .claude_tasks/task_morning_exercise/task_plan.md  # 刷新目标
Read .claude_tasks/task_morning_exercise/notes.md      # 获取发现
Write .claude_tasks/task_morning_exercise/exercise_summary.md
Edit .claude_tasks/task_morning_exercise/task_plan.md  # 标记阶段3完成
```

### 循环4：交付
```bash
Read .claude_tasks/task_morning_exercise/task_plan.md  # 验证完成
Deliver .claude_tasks/task_morning_exercise/exercise_summary.md
```

---

## 示例2：Bug修复任务

**用户请求：** "修复身份验证模块中的登录bug"

**目录：** `.claude_tasks/task_login_bug/`

### task_plan.md（修复完成后）
```markdown
# 任务计划：修复登录bug

## 目标
识别并修复阻止成功登录的bug。

## 阶段
- [x] 阶段1：理解bug报告 ✓
- [x] 阶段2：定位相关代码 ✓
- [x] 阶段3：识别根本原因 ✓
- [x] 阶段4：实现修复 ✓
- [x] 阶段5：代码Review ✓
- [x] 阶段6：测试和验证 ✓

## 关键问题
1. 出现什么错误信息？
2. 哪个文件处理身份验证？
3. 最近发生了什么变化？

## 已做决策
- 身份验证处理程序在 src/auth/login.ts
- 错误发生在 validateToken() 函数

## 遇到的错误
- [初始] TypeError: 无法读取undefined的'token'属性
  → 根本原因：user对象未正确等待

## 代码Review
- [x] 语法和逻辑检查 - 通过
- [x] 项目规范符合性检查 - 通过
- [x] 用户需求符合性检查 - 通过
- Review结论：通过
  - 添加了 async/await 正确处理 Promise
  - 遵循项目命名约定
  - 边界情况已处理（null user）

## 状态
**任务完成** - Bug已修复并验证
```

---

## 示例3：功能开发

**用户请求：** "在设置页面添加深色模式切换"

**目录：** `.claude_tasks/task_dark_mode/`

### 三文件模式的应用

**task_plan.md:**
```markdown
# 任务计划：深色模式切换

## 目标
在设置中添加功能性的深色模式切换。

## 阶段
- [x] 阶段1：研究现有主题系统 ✓
- [x] 阶段2：设计实现方法 ✓
- [x] 阶段3：实现切换组件 ✓
- [x] 阶段4：添加主题切换逻辑 ✓
- [x] 阶段5：代码Review ✓
- [x] 阶段6：测试和完善 ✓

## 已做决策
- 使用CSS自定义属性作为主题
- 在localStorage中存储偏好
- 切换组件在SettingsPage.tsx中

## 代码Review
- [x] 语法和逻辑检查 - 通过
- [x] 项目规范符合性检查 - 通过
- [x] 用户需求符合性检查 - 通过
- Review结论：通过
  - 遵循 React Hooks 最佳实践
  - 颜色值符合设计系统规范
  - localStorage 读写正确处理异常
  - 无障碍属性（aria-label）已添加

## 状态
**任务完成** - 深色模式功能已实现并测试
```

**notes.md:**
```markdown
# 笔记：深色模式实现

## 现有主题系统
- 位于：src/styles/theme.ts
- 使用：CSS自定义属性
- 当前主题：仅浅色

## 需要修改的文件
1. src/styles/theme.ts - 添加深色主题颜色
2. src/components/SettingsPage.tsx - 添加切换
3. src/hooks/useTheme.ts - 创建新hook
4. src/App.tsx - 用ThemeProvider包装

## 颜色决策
- 深色背景：#1a1a2e
- 深色表面：#16213e
- 深色文本：#eaeaea
```

**dark_mode_implementation.md:** (交付物)
```markdown
# 深色模式实现

## 所做的更改

### 1. 添加了深色主题颜色
文件：src/styles/theme.ts
...

### 2. 创建了useTheme hook
文件：src/hooks/useTheme.ts
...
```

---

## 示例4：错误恢复模式

当某件事失败时，**不要**隐藏它：

### 之前（错误）
```
操作：读取 config.json
错误：文件未找到
操作：读取 config.json  # 静默重试
操作：读取 config.json  # 又一次重试
```

### 之后（正确）
```
操作：读取 config.json
错误：文件未找到

# 更新 .claude_tasks/task_config_fix/task_plan.md：
## 遇到的错误
- config.json 未找到 → 将创建默认配置

操作：Write config.json（默认配置）
操作：Read config.json
成功！
```

---

## 阅读后决策模式

**在重大决策前始终阅读你的计划：**

```
[已经发生许多工具调用...]
[上下文正在变长...]
[原始目标可能被遗忘...]

→ Read .claude_tasks/task_xxxx/task_plan.md  # 这让目标重新进入注意力！
→ 现在做出决策                                # 目标在上下文中是新鲜的
```

这就是Manus可以在~50个工具调用中保持跟踪的原因。计划文件充当"目标刷新"机制。

---

## 示例5：代码Review完整流程

**用户请求：** "重构用户API模块，添加缓存机制"

**目录：** `.claude_tasks/task_user_api_cache/`

### 完整工作流程（含代码Review）

#### 代码修改完成后

```bash
# 完成所有代码修改后，进行Review
Read .claude_tasks/task_user_api_cache/task_plan.md  # 刷新目标
Read src/api/user.ts                                  # 阅读修改后的代码
```

#### 代码Review检查项

**1. 语法和逻辑检查**
- [ ] 无语法错误
- [ ] 无类型错误（TypeScript）
- [ ] 逻辑流程正确
- [ ] 无潜在的运行时错误
- [ ] 异常处理完善

**2. 项目规范符合性检查**
- [ ] 遵循代码风格指南
- [ ] 命名约定一致
- [ ] 文件目录结构正确
- [ ] 导入语句规范
- [ ] 注释和文档完整

**3. 用户需求符合性检查**
- [ ] 功能完整性
- [ ] 边界条件处理
- [ ] 性能要求满足
- [ ] 向后兼容性

#### 更新 task_plan.md

```bash
Edit .claude_tasks/task_user_api_cache/task_plan.md  # 记录Review结果
```

#### task_plan.md（Review后状态）

```markdown
# 任务计划：用户API缓存重构

## 目标
重构用户API模块，添加缓存机制提升性能。

## 阶段
- [x] 阶段1：分析现有代码 ✓
- [x] 阶段2：设计缓存策略 ✓
- [x] 阶段3：实现缓存层 ✓
- [x] 阶段4：重构API调用 ✓
- [x] 阶段5：代码Review ✓
- [ ] 阶段6：测试和验证（当前）

## 代码Review

### 语法和逻辑检查
- [x] 无语法错误
- [x] TypeScript类型正确
- [x] 缓存逻辑正确（TTL、失效策略）
- [x] Promise链正确处理
- [ ] 异常处理需完善
  → **问题**：缓存失败时缺少降级方案
  → **修正**：添加 try-catch，缓存失败时直接调用API

### 项目规范符合性检查
- [x] 遵循ESLint规则
- [x] 命名约定：驼峰式 + 类型后缀
- [x] 文件位置：src/api/user.ts
- [x] 导入语句：使用绝对路径
- [ ] 缺少JSDoc注释
  → **修正**：为导出函数添加文档注释

### 用户需求符合性检查
- [x] 缓存功能完整
- [x] 缓存失效策略正确
- [x] 性能提升明显（约50%）
- [x] 保持向后兼容

### Review结论
**需要修正**后通过
1. 添加缓存失败的降级处理
2. 补充JSDoc文档注释

## 状态
**当前处于阶段6** - 修正Review发现的问题并测试
```

#### 修正问题后再次Review

```bash
# 修正完成后再次Review
Edit .claude_tasks/task_user_api_cache/task_plan.md
```

```markdown
## 代码Review（更新）

### Review结论
**通过**
1. ✅ 已添加缓存失败降级处理
2. ✅ 已补充完整JSDoc注释

## 状态
**任务完成** - 重构已完成并通过Review
```
