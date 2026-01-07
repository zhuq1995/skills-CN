# 设计文档 - 计数器功能

## 概述

本设计描述了一个简单的计数器组件,支持数值增减、重置、历史记录和主题切换。

## 架构

```
┌─────────────────────────────────────────┐
│            CounterComponent              │
├─────────────────────────────────────────┤
│                                         │
│  ┌─────────────────────────────────┐    │
│  │        显示区域                  │    │
│  │        当前计数值                │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ┌───────┬───────┬───────┬───────────┐  │
│  │  (-)  │  (+)  │ 重置  │ 主题切换  │  │
│  └───────┴───────┴───────┴───────────┘  │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │        历史记录列表              │    │
│  └─────────────────────────────────┘    │
│                                         │
└─────────────────────────────────────────┘
```

## 组件和接口

### 1. Counter.vue 组件

#### Props

```typescript
interface CounterProps {
  initialValue?: number    // 初始值，默认 0
  maxValue?: number        // 最大值，默认 100
  minValue?: number        // 最小值，默认 0
  enableHistory?: boolean  // 是否启用历史记录，默认 true
  enableTheme?: boolean    // 是否启用主题切换，默认 true
}
```

#### Emits

```typescript
interface CounterEmits {
  change: (value: number) => void
  reset: () => void
  themeChange: (theme: 'light' | 'dark') => void
}
```

#### 组件结构

```vue
<template>
  <div class="counter" :class="{ 'counter--dark': isDark }">
    <!-- 显示区域 -->
    <div class="counter__display">
      <span class="counter__value">{{ displayValue }}</span>
    </div>

    <!-- 操作按钮 -->
    <div class="counter__actions">
      <button
        class="counter__btn counter__btn--decrement"
        :disabled="value <= minValue"
        @click="handleDecrement"
      >
        -
      </button>
      <button
        class="counter__btn counter__btn--increment"
        :disabled="value >= maxValue"
        @click="handleIncrement"
      >
        +
      </button>
      <button
        class="counter__btn counter__btn--reset"
        @click="handleReset"
      >
        重置
      </button>
      <button
        v-if="enableTheme"
        class="counter__btn counter__btn--theme"
        @click="toggleTheme"
      >
        {{ isDark ? '浅色' : '深色' }}
      </button>
    </div>

    <!-- 历史记录 -->
    <div v-if="enableHistory" class="counter__history">
      <h4>历史记录</h4>
      <ul>
        <li v-for="record in history" :key="record.id">
          {{ formatTime(record.timestamp) }} - {{ record.action }}: {{ record.value }}
        </li>
      </ul>
      <button @click="clearHistory">清除历史</button>
    </div>
  </div>
</template>
```

### 2. useCounter 组合式函数

#### 接口定义

```typescript
interface UseCounterOptions {
  initialValue?: number
  maxValue?: number
  minValue?: number
}

interface UseCounterReturn {
  value: Ref<number>
  displayValue: ComputedRef<string>
  isMax: ComputedRef<boolean>
  isMin: ComputedRef<boolean>
  increment: () => void
  decrement: () => void
  reset: () => void
  setValue: (val: number) => void
}
```

#### 实现概要

```javascript
export function useCounter(options = {}) {
  const {
    initialValue = 0,
    maxValue = 100,
    minValue = 0
  } = options

  const value = ref(initialValue)
  const history = ref([])

  const displayValue = computed(() => value.value.toString().padStart(3, '0'))
  const isMax = computed(() => value.value >= maxValue)
  const isMin = computed(() => value.value <= minValue)

  const increment = () => {
    if (!isMax.value) {
      value.value++
      addHistory('增加', value.value)
      playSound()
    }
  }

  const decrement = () => {
    if (!isMin.value) {
      value.value--
      addHistory('减少', value.value)
      playSound()
    }
  }

  const reset = () => {
    value.value = initialValue
    history.value = []
    addHistory('重置', value.value)
    playSound()
  }

  const addHistory = (action, val) => {
    history.value.unshift({
      id: Date.now(),
      timestamp: Date.now(),
      action,
      value: val
    })
    if (history.value.length > 50) {
      history.value = history.value.slice(0, 50)
    }
  }

  return {
    value,
    displayValue,
    isMax,
    isMin,
    increment,
    decrement,
    reset,
    setValue: (val) => { value.value = val }
  }
}
```

## 数据模型

```typescript
interface HistoryRecord {
  id: number
  timestamp: number
  action: '增加' | '减少' | '重置'
  value: number
}

interface CounterState {
  value: number
  theme: 'light' | 'dark'
  history: HistoryRecord[]
}
```

## 正确性属性

### 属性 1: 边界值约束

*对于任意*有效的初始值、最大值和最小值配置,操作后的值应该始终在 [minValue, maxValue] 范围内

**验证: 需求 2.3, 3.3**

### 属性 2: 增量一致性

*对于任意*在有效范围内的当前值,执行 increment 后值应该增加 1

**验证: 需求 2.1**

### 属性 3: 减量一致性

*对于任意*在有效范围内的当前值,执行 decrement 后值应该减少 1

**验证: 需求 3.1**

### 属性 4: 重置正确性

*对于任意*当前值,执行 reset 后值应该等于初始值且历史记录为空

**验证: 需求 4.1, 4.2**

### 属性 5: 历史记录完整性

*对于任意*数值变化操作,历史记录应该包含对应的变更记录且条数不超过50条

**验证: 需求 5.1, 5.3**

## 错误处理

### 错误类型

1. **越界错误**: 数值超出 minValue 或 maxValue 范围
2. **类型错误**: 传入的参数类型不正确
3. **状态错误**: 状态保存/恢复失败

### 处理策略

```javascript
// 越界错误防护
const setValue = (val) => {
  if (typeof val !== 'number') {
    console.error('[Counter] 无效的值类型:', val)
    return
  }
  const clamped = Math.max(minValue, Math.min(maxValue, val))
  value.value = clamped
}

// 边界操作防护
const increment = () => {
  if (isMax.value) {
    console.warn('[Counter] 已达到最大值，无法继续增加')
    return
  }
  value.value++
}
```
