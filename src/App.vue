<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref('')

const todos_asc = computed(() => todos.value.sort((a, b) => b.createdAt - a.createdAt))

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) return

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: Date.now()
  })

  input_content.value = ''
}

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo)
}

watch(todos, (newTodos) => {
  localStorage.setItem('todos', JSON.stringify(newTodos))
}, { deep: true })

watch(name, (newName) => {
  localStorage.setItem('name', newName)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" v-model="name" placeholder="Name here" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>
        CREATE A TODO
      </h3>

      <form @submit.prevent="addTodo">
        <h4>What on your todo list?</h4>
        <input 
          type="text" 
          v-model="input_content" 
          placeholder="e.g Buy some cookies" />
        
        <h4>What category is it?</h4>

        <div class="options">
          <label>
            <input 
              type="radio" 
              name="category" 
              value="work" 
              checked="true"
              v-model="input_category" />
            <span class="bubble business"></span>
            <div>business</div>
          </label>

          <label>
            <input 
              type="radio" 
              name="category" 
              value="personal" 
              v-model="input_category" />
            <span class="bubble personal"></span>
            <div>personal</div>
          </label>

        </div>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>

          <div class="actions">
            <button class="delete" @click=removeTodo(todo)>Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
