<template>
  <v-container class="fill-height" max-width="400">
    <v-card class="pa-6 w-100">
      <v-card-title>Login</v-card-title>
      <v-text-field v-model="email" label="Email" type="email" />
      <v-text-field v-model="password" label="Password" type="password" />
      <v-alert v-if="error" type="error" class="mb-4">{{ error }}</v-alert>
      <v-btn color="primary" block @click="login">Login</v-btn>
      <v-btn variant="text" block class="mt-2" to="/register">Create an account</v-btn>
    </v-card>
  </v-container>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { API_URL } from '@/config'

const router = useRouter()
const email = ref('')
const password = ref('')
const error = ref('')

async function login () {
  error.value = ''
  const res = await fetch(`${API_URL}/auth/login`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email: email.value, password: password.value }),
  })
  const data = await res.json()
  if (!res.ok) {
    error.value = data.message
    return
  }
  localStorage.setItem('token', data.token)
  router.push('/')
}
</script>
