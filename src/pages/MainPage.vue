<template>
  <div class="page">
    <img src="../assets/icons/logo.svg" />
    <AppInput
      :selectedUser="selectedUser"
      @select="handleSelect"
      @clear="clearUser"
    />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import AppInput from '../components/AppInput.vue'

interface User {
    id: number
    name: string
    username: string
    email: string
}

const selectedUser = ref<User | null>(null)

function handleSelect(user: User) {
  selectedUser.value = user
}

function clearUser() {
  selectedUser.value = null
}
</script>

<style scoped>
@import '../styles/variables.css';

.page {
  background-color: var(--dark-gray);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 56px;
  height: 100vh;
}
</style>
