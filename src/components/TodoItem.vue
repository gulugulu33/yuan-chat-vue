<script setup>
// 【Vue核心概念：Props】
// 定义组件的props，用于接收父组件传递的数据
// 这里我们定义了两个prop：
// 1. todo: 待办事项对象，必须提供
// 2. is-selected: 是否被选中，默认值为false
const props = defineProps({
  todo: {
    type: Object,
    required: true
  },
  isSelected: {
    type: Boolean,
    default: false
  }
})

// 【Vue核心概念：Emits】
// 定义组件的事件，用于向父组件发送消息
// 这里我们定义了三个事件：delete、toggle和toggle-selection
const emit = defineEmits(['delete', 'toggle', 'toggle-selection'])

// 【Vue核心概念：事件处理】
// 删除待办事项的方法
// 当点击删除按钮时，通过emit向父组件发送delete事件，并传递todo的id
const handleDelete = () => {
  emit('delete', props.todo.id)
}

// 切换待办事项完成状态的方法
// 当点击完成状态复选框时，通过emit向父组件发送toggle事件，并传递todo的id
const handleToggle = () => {
  emit('toggle', props.todo.id)
}

// 【新增功能：切换选中状态】
// 切换待办事项的选中状态
// 当点击选中复选框时，通过emit向父组件发送toggle-selection事件，并传递todo的id
const handleToggleSelection = () => {
  emit('toggle-selection', props.todo.id)
}
</script>

<template>
  <div class="todo-item" :class="{ completed: todo.completed, selected: isSelected }">
    <!-- 【新增功能：选中复选框】 -->
    <input 
      type="checkbox" 
      class="select-checkbox"
      :checked="isSelected" 
      @change="handleToggleSelection"
    />
    
    <!-- 完成状态复选框 -->
    <input 
      type="checkbox" 
      class="complete-checkbox"
      :checked="todo.completed" 
      @change="handleToggle"
    />
    
    <span class="todo-text">{{ todo.text }}</span>
    <button class="delete-btn" @click="handleDelete">删除</button>
  </div>
</template>

<style scoped>
.todo-item {
  display: flex;
  align-items: center;
  padding: 10px;
  border-bottom: 1px solid #eee;
  transition: background-color 0.3s;
}

/* 【新增样式：选中状态】 */
.todo-item.selected {
  background-color: #e8f5e8;
}

.todo-item.completed .todo-text {
  text-decoration: line-through;
  color: #999;
}

/* 【新增样式：复选框间距】 */
.select-checkbox, .complete-checkbox {
  margin-right: 10px;
  cursor: pointer;
}

.todo-text {
  flex: 1;
  margin: 0 10px;
}

.delete-btn {
  padding: 5px 10px;
  background-color: #ff4444;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.delete-btn:hover {
  background-color: #cc0000;
}
</style>