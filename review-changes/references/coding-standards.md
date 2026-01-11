# 代码审查标准参考

## 项目代码风格要求 (来自 CLAUDE.md)

### Vue 3 组合式 API（强制）
```javascript
// ✅ 正确
<script setup>
import { ref, computed } from 'vue'
const count = ref(0)
</script>

// ❌ 禁止
<script>
export default {
  data() { return { count: 0 } }
}
</script>
```

### JavaScript 风格
- 使用 2 空格缩进
- 字符串使用单引号
- 不使用分号
- 代码注释使用中文
- 使用 `const`/`let`，禁止使用 `var`

### 导入顺序
```javascript
// 1. Vue 导入
import { ref, computed, onMounted } from 'vue'

// 2. 外部库
import axios from 'axios'

// 3. 内部导入（使用 @ 别名）
import { usePluginBase } from '@/composables/usePluginBase.js'
import { createMessage } from '@/types/chat.js'
```

## 常见审查问题清单

### 代码质量问题
- [ ] 函数过长（建议 < 50 行）
- [ ] 圈复杂度过高
- [ ] 重复代码片段
- [ ] 变量/函数命名不清晰
- [ ] 缺少适当的注释

### 安全问题
- [ ] 用户输入未验证
- [ ] SQL/NoSQL 注入风险
- [ ] XSS 跨站脚本
- [ ] 敏感信息硬编码
- [ ] 认证/授权缺失

### 错误处理
- [ ] 缺少 try-catch
- [ ] 错误被静默吞掉
- [ ] 没有适当的错误提示
- [ ] 资源未正确释放

### 性能问题
- [ ] 不必要的循环/嵌套
- [ ] 缺少必要的缓存
- [ ] 大数据未分页
- [ ] 同步阻塞操作

### 测试覆盖
- [ ] 关键逻辑缺少单元测试
- [ ] 边界条件未覆盖
- [ ] 错误场景未测试

## 插件系统审查要点

对于 `frontend/src/plugins/practices/` 下的插件修改：

- [ ] 必须使用 `<script setup>` 语法
- [ ] 使用 `usePluginBase()` 进行状态管理
- [ ] 配置是自包含的（无外部配置文件）
- [ ] 遵循插件定义模式 (`definePlugin`)
- [ ] 聊天历史通过 `useProgressSync` 自动保存

## API 变更审查要点

- [ ] RESTful 风格一致
- [ ] 认证中间件正确使用
- [ ] 参数验证完整
- [ ] 错误响应格式统一
- [ ] 文档/注释更新
