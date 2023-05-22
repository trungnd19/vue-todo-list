<script setup>
import { onMounted, ref, watch, computed } from 'vue'
import { uid } from 'uid'

import TodoCreator from '../components/TodoCreator.vue'
import TodoItem from '../components/TodoItem.vue'
import { Icon } from '@iconify/vue'

const todoList = ref([])

watch(todoList, () => {
  setTodoListLocalStorage()
}, { deep: true })

const todoCompleted = computed(() => {
  return todoList.value.every(item => item.isCompleted)
})

function createTodo(todo) {
  todoList.value.push({
    id: uid(),
    todo: todo,
    isCompleted: false,
    isEditing: false
  })
}

function toggleTodoComplete(idx) {
  todoList.value[idx].isCompleted = !todoList.value[idx].isCompleted
}

function toggleEditTodo(idx) {
  todoList.value[idx].isEditing = !todoList.value[idx].isEditing
}

function updateTodo(todoContent, idx) {
  todoList.value[idx].todo = todoContent
}

function deleteTodo(id) {
  const idx = todoList.value.findIndex((item) => item.id === id)
  todoList.value.splice(idx, 1)
}

function setTodoListLocalStorage() {
  localStorage.setItem('todoList', JSON.stringify(todoList.value))
}

function fetchTodoList() {
  const savedTodoList = JSON.parse(localStorage.getItem('todoList'))
  if (savedTodoList) {
    todoList.value = savedTodoList
  }
}

onMounted(() => {
  fetchTodoList()
})
</script>

<template>
  <main>
    <h1>Create Todo</h1>
    <TodoCreator @create-todo="createTodo" />
    <ul class="todo-list" v-if="todoList.length">
      <TodoItem
        v-for="(todo, index) in todoList"
        :key="todo.id"
        :todo="todo"
        :index="index"
        @toggle-complete="toggleTodoComplete"
        @edit-todo="toggleEditTodo"
        @update-todo="updateTodo"
        @delete-todo="deleteTodo"
      />
    </ul>
    <p class="todos-msg" v-else>
      <Icon icon="noto-v1:sad-but-relieved-face" />
      <span>You have no todo's to complete! Add one!</span>
    </p>
    <p v-if="todoCompleted && todoList.length > 0" class="todos-msg">
      <Icon icon="noto-v1:party-popper"/>
      <span>You have completed all your todos!</span>
    </p>
  </main>
</template>

<style lang="scss" scoped>
main {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
  padding: 40px 16px;

  h1 {
    margin-bottom: 16px;
    text-align: center;
  }

  .todo-list {
    display: flex;
    flex-direction: column;
    list-style: none;
    margin-top: 24px;
    gap: 20px;
  }

  .todos-msg {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 24px;
  }
}
</style>
