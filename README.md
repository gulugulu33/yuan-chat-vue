# Vue Todo List 应用教学

这是一个基于 Vue 3 + Vite 的 Todo List 应用，用于教学 Vue 核心概念和组件化开发。

## 目录结构

```
src/
├── components/          # 组件目录
│   └── TodoItem.vue    # 待办事项组件
├── App.vue             # 根组件
├── main.js             # 应用入口
└── style.css           # 全局样式
```

## Vue 核心概念讲解

### 1. 组件化开发

Vue 采用组件化开发思想，将应用拆分为可复用的组件。

- **App.vue**: 根组件，管理整个应用的状态和逻辑
- **TodoItem.vue**: 待办事项组件，负责单个待办事项的展示和交互

### 2. 响应式数据 (Reactivity)

Vue 3 使用 `ref` 和 `reactive` 函数创建响应式数据。

```javascript
// 在 App.vue 中
import { ref } from 'vue'

// 创建响应式数组
const todos = ref([
  { id: 1, text: '学习 Vue 基础', completed: false }
])

// 创建响应式字符串
const newTodo = ref('')
```

### 3. 模板语法和指令

#### v-model (双向数据绑定)

```html
<input type="text" v-model="newTodo" placeholder="添加新的待办事项..." />
```

#### v-for (列表渲染)

```html
<TodoItem 
  v-for="todo in todos" 
  :key="todo.id"
  :todo="todo"
  @delete="deleteTodo"
  @toggle="toggleTodo"
/>
```

#### v-on 或 @ (事件监听)

```html
<button @click="addTodo">添加</button>
<input @keyup.enter="addTodo" />
<input type="checkbox" @change="handleToggle" />
```

#### :class (动态类绑定)

```html
<div class="todo-item" :class="{ completed: todo.completed }">
```

### 4. 组件通信

#### Props (父组件向子组件传递数据)

```javascript
// 在 TodoItem.vue 中
const props = defineProps({
  todo: {
    type: Object,
    required: true
  }
})
```

```html
<!-- 在 App.vue 中 -->
<TodoItem :todo="todo" />
```

#### Emits (子组件向父组件发送事件)

```javascript
// 在 TodoItem.vue 中
const emit = defineEmits(['delete', 'toggle'])

const handleDelete = () => {
  emit('delete', props.todo.id)
}
```

```html
<!-- 在 App.vue 中 -->
<TodoItem @delete="deleteTodo" @toggle="toggleTodo" />
```

### 5. 计算属性 (Computed Properties)

可以用于计算派生数据，例如统计剩余待办事项数量：

```javascript
import { computed } from 'vue'

const remainingTodos = computed(() => {
  return todos.value.filter(todo => !todo.completed).length
})
```

### 6. 生命周期钩子 (Lifecycle Hooks)

Vue 组件有多个生命周期阶段，可以在不同阶段执行代码：

- `onMounted`: 组件挂载后执行
- `onUpdated`: 组件更新后执行
- `onUnmounted`: 组件卸载前执行

## 功能实现

### 1. 添加待办事项

```javascript
const addTodo = () => {
  if (newTodo.value.trim() !== '') {
    todos.value.push({
      id: Date.now(),
      text: newTodo.value.trim(),
      completed: false
    })
    newTodo.value = ''
  }
}
```

### 2. 删除待办事项

```javascript
const deleteTodo = (id) => {
  todos.value = todos.value.filter(todo => todo.id !== id)
}
```

### 3. 切换待办事项完成状态

```javascript
const toggleTodo = (id) => {
  const todo = todos.value.find(todo => todo.id === id)
  if (todo) {
    todo.completed = !todo.completed
  }
}
```

## 运行项目

1. 安装依赖

```bash
npm install
```

2. 启动开发服务器

```bash
npm run dev
```

3. 构建生产版本

```bash
npm run build
```

## 学习资源

- [Vue 3 官方文档](https://vuejs.org/)
- [Vite 官方文档](https://vitejs.dev/)
- [Vue 组件化开发指南](https://vuejs.org/guide/essentials/component-basics.html)

## 扩展练习

1. 添加编辑待办事项功能
2. 实现待办事项过滤（全部/已完成/未完成）
3. 添加本地存储功能，保存待办事项
4. 实现待办事项拖拽排序
5. 添加动画效果