<script setup>
// 【Vue核心概念：Composition API】
// 导入Vue的ref函数，用于创建响应式数据
import { ref, computed } from 'vue'
// 导入子组件TodoItem
import TodoItem from './components/TodoItem.vue'

// 【Vue核心概念：响应式数据】
// 使用ref创建响应式数组，用于存储待办事项列表
// 初始数据包含3个待办事项
const todos = ref([
  { id: 1, text: '学习 Vue 基础', completed: false },
  { id: 2, text: '创建 Todo List 应用', completed: false },
  { id: 3, text: '掌握组件化开发', completed: false }
])

// 使用ref创建响应式字符串，用于存储新待办事项的输入内容
const newTodo = ref('')

// 【新增功能：多选删除】
// 使用ref创建响应式Set，用于存储选中的待办事项ID
const selectedTodoIds = ref(new Set())

// 计算属性：是否所有待办事项都被选中
const isAllSelected = computed(() => {
  return todos.value.length > 0 && selectedTodoIds.value.size === todos.value.length
})

// 计算属性：是否有任意待办事项被选中
const hasSelectedTodos = computed(() => {
  return selectedTodoIds.value.size > 0
})

// 【Vue核心概念：方法】
// 添加待办事项的方法
const addTodo = () => {
  // 验证输入内容不为空
  if (newTodo.value.trim() !== '') {
    // 向todos数组中添加新的待办事项
    // 使用Date.now()生成唯一id
    todos.value.push({
      id: Date.now(),
      text: newTodo.value.trim(),
      completed: false
    })
    // 清空输入框
    newTodo.value = ''
  }
}

// 删除单个待办事项的方法
// 通过id过滤掉需要删除的待办事项
const deleteTodo = (id) => {
  todos.value = todos.value.filter(todo => todo.id !== id)
  // 从选中列表中移除已删除的id
  selectedTodoIds.value.delete(id)
}

// 【新增功能：批量删除】
// 批量删除选中的待办事项
const batchDeleteTodos = () => {
  // 过滤掉选中的待办事项
  todos.value = todos.value.filter(todo => !selectedTodoIds.value.has(todo.id))
  // 清空选中列表
  selectedTodoIds.value.clear()
}

// 【新增功能：切换选中状态】
// 切换单个待办事项的选中状态
const toggleTodoSelection = (id) => {
  if (selectedTodoIds.value.has(id)) {
    selectedTodoIds.value.delete(id)
  } else {
    selectedTodoIds.value.add(id)
  }
}

// 【新增功能：全选/取消全选】
// 切换所有待办事项的选中状态
const toggleAllSelection = () => {
  if (isAllSelected.value) {
    // 取消全选
    selectedTodoIds.value.clear()
  } else {
    // 全选
    selectedTodoIds.value = new Set(todos.value.map(todo => todo.id))
  }
}

// 切换待办事项完成状态的方法
const toggleTodo = (id) => {
  // 根据id查找对应的待办事项
  const todo = todos.value.find(todo => todo.id === id)
  if (todo) {
    // 切换completed属性的值
    todo.completed = !todo.completed
  }
}
</script>

<template>
  <div class="todo-app">
    <h1>Todo List 应用</h1>
    <div class="todo-input">
      <input 
        type="text" 
        v-model="newTodo" 
        placeholder="添加新的待办事项..."
        @keyup.enter="addTodo"
      />
      <button @click="addTodo">添加</button>
    </div>
    
    <!-- 【新增功能：批量操作栏】 -->
    <div class="todo-actions" v-if="todos.length > 0">
      <div class="select-all">
        <input 
          type="checkbox" 
          id="select-all" 
          :checked="isAllSelected" 
          @change="toggleAllSelection"
        />
        <label for="select-all">全选</label>
      </div>
      
      <div class="batch-actions">
        <button 
          class="batch-delete-btn"
          @click="batchDeleteTodos"
          :disabled="!hasSelectedTodos"
        >
          删除选中 ({{ selectedTodoIds.size }})
        </button>
      </div>
    </div>
    
    <div class="todo-list">
      <TodoItem 
        v-for="todo in todos" 
        :key="todo.id"
        :todo="todo"
        :is-selected="selectedTodoIds.has(todo.id)"
        @delete="deleteTodo"
        @toggle="toggleTodo"
        @toggle-selection="toggleTodoSelection"
      />
    </div>
    <div class="todo-stats">
      <span>剩余: {{ todos.filter(todo => !todo.completed).length }}</span>
    </div>
  </div>
</template>

<style scoped>
.todo-app {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

h1 {
  text-align: center;
  color: #333;
}

.todo-input {
  display: flex;
  margin-bottom: 20px;
}

.todo-input input {
  flex: 1;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ddd;
  border-radius: 4px 0 0 4px;
}

.todo-input button {
  padding: 10px 20px;
  font-size: 16px;
  background-color: #42b883;
  color: white;
  border: none;
  border-radius: 0 4px 4px 0;
  cursor: pointer;
}

.todo-input button:hover {
  background-color: #35495e;
}

/* 【新增样式：批量操作栏】 */
.todo-actions {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
  padding: 10px;
  background-color: #f8f9fa;
  border-radius: 4px;
}

.select-all {
  display: flex;
  align-items: center;
  gap: 5px;
}

.batch-delete-btn {
  background-color: #ff4444;
}

.batch-delete-btn:hover:not(:disabled) {
  background-color: #cc0000;
}

.batch-delete-btn:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.todo-list {
  margin-bottom: 20px;
}

.todo-stats {
  text-align: center;
  color: #666;
  font-size: 14px;
}
</style>
