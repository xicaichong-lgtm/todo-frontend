<template>
  <v-container max-width="600">
    <div class="d-flex align-center mb-4">
      <h2>My TODOs</h2>
      <v-spacer />
      <v-btn variant="text" @click="logout">Logout</v-btn>
    </div>

    <div class="d-flex ga-2 mb-4">
      <v-text-field v-model="newTitle" label="New TODO" hide-details @keyup.enter="addTodo" />
      <v-btn color="primary" height="56" @click="addTodo">Add</v-btn>
    </div>

    <v-card>
      <v-list>
        <v-list-item v-for="todo in todos" :key="todo.id">
         <template #prepend>
           <v-checkbox-btn :model-value="todo.done" @update:model-value="toggleTodo(todo)" />
         </template>
         <v-list-item-title :class="{ 'text-decoration-line-through': todo.done }">
           {{ todo.title }}
         </v-list-item-title>
         <template #append>
           <v-btn variant="text" color="error" @click="deleteTodo(todo)">Delete</v-btn>
         </template>
        </v-list-item>
      </v-list>
      <v-progress-linear v-if="loading" indeterminate />
      <v-card-text v-if="!loading && todos.length === 0">No TODOs yet</v-card-text>
    </v-card>
  </v-container>
</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'

interface Todo {
  id: number
  title: string
  done: boolean
}

const router = useRouter()
const todos = ref<Todo[]>([])
const loading = ref(true)
const newTitle = ref('')

async function loadTodos () {
  const token = localStorage.getItem('token')

  if (!token) {
    router.push('/login')
    return
  }
  const res = await fetch('http://localhost:3000/todos', {
    headers: { Authorization: `Bearer ${token}` },
  })
  if (res.status === 401) {
    localStorage.removeItem('token')
    router.push('/login')
    return
  }
  todos.value = await res.json()
  loading.value = false
}

async function addTodo () {
  const title = newTitle.value.trim()
  if (!title) return

  const token = localStorage.getItem('token')
  const res = await fetch('http://localhost:3000/todos', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${token}`,
    },
    body: JSON.stringify({ title }),
  })
  if (res.status === 401) {
    localStorage.removeItem('token')
    router.push('/login')
    return
  }
  const todo = await res.json()
  todos.value.push(todo)
  newTitle.value = ''
}

async function toggleTodo (todo: Todo) {
  const token = localStorage.getItem('token')
  const res = await fetch(`http://localhost:3000/todos/${todo.id}`, {
    method: 'PATCH',
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${token}`,
    },
    body: JSON.stringify({ done: !todo.done }),
  })
  if (res.status === 401) {
    localStorage.removeItem('token')
    router.push('/login')
    return
  }
  if (res.ok) {
    const updated = await res.json()
    todo.done = updated.done
  }
}

async function deleteTodo (todo: Todo) {
  const token = localStorage.getItem('token')
  const res = await fetch(`http://localhost:3000/todos/${todo.id}`, {
    method: 'DELETE',
    headers: { Authorization: `Bearer ${token}` },
  })
  if (res.status === 401) {
    localStorage.removeItem('token')
    router.push('/login')
    return
  }

  if (res.ok) {
    todos.value = todos.value.filter(t => t.id !== todo.id)
  }
}

function logout () {
  localStorage.removeItem('token')
  router.push('/login')
}

onMounted(loadTodos)
</script>