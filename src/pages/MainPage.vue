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

const selectedUser = ref(null)

function handleSelect(user: any) {
  selectedUser.value = user
}

function clearUser() {
  selectedUser.value = null
}
</script>

<style scoped>
.page {
  background-color: #323230;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 56px;
  margin-top: 300px;
}
</style>
