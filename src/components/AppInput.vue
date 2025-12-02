<template>
  <div class="wrapper">
    <img class="icon" src="../assets/icons/logo2.svg" />

    <div v-if="selectedUser" class="tag">
      {{ selectedUser.name }}
      <img class="close-icon" @click="clear" src="../assets/icons/close-circle.svg" />
    </div>

    <input
      v-model="query"
      @input="getUsers"
      :disabled="!!selectedUser"
      :placeholder="selectedUser ? '' : 'Введите запрос'"
    />

    <img v-if="!selectedUser" class="icon" src="../assets/icons/search.svg" />

    <ul v-if="!selectedUser && list.length" class="dropdown">
      <li
        v-for="item in list"
        :key="item.id"
        @click="select(item)"
      >
        {{ item.name }}
      </li>
    </ul>
  </div>
</template>
<script setup lang="ts">
import { ref } from 'vue'

interface User {
  id: number
  name: string
  username: string
  email: string
}

const props = defineProps<{
  selectedUser: User | null
}>()
const emit = defineEmits<{
  (e: 'select', user: User): void
  (e: 'clear'): void
}>()

const query = ref('')
const list = ref<User[]>([])
const isLoading = ref(false)
const apiBase = 'https://apimocker.com'

async function getUsers() {
  if (!query.value) {
    list.value = []
    return
  }

  try {
    isLoading.value = true;
    const q = encodeURIComponent(query.value)
    const res = await fetch(`${apiBase}/users/search?q=${q}`)
    
    if (!res.ok) {
      list.value = []
      return
    }

    const data = await res.json()
    const results = data?.results ?? []
    list.value = results.map((r: User) => ({
      id: r.id,
      name: r.name,
      username: r.username,
      email: r.email
    }))
  } catch (err) {
    console.error('error', err)
    list.value = []
  } finally {
    isLoading.value = false;
  }
}

function select(user: User) {
  emit('select', user)
  query.value = ''
  list.value = []
}

function clear() {
  emit('clear')
}

</script>

<style scoped>
@import '../styles/variables.css';

.wrapper {
  position: relative;
  width: calc(90vw - 800px);
  min-width: 450px;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  background: var(--background);
  border-radius: 10px;
  padding: 0 30px;
}

input {
  width: 100%;
  height: 80px;
  color: var(--white);
  background: var(--background);
  padding-left: 30px;
  font-size: 15px;
  border: none;
  outline: var(--black);
}

.icon {
  width: 40px;
  height: 40px;
}

.dropdown {
  position: absolute;
  top: 100px;
  width: calc(90vw - 800px);
  min-width: 450px;
  background: var(--light-gray);
  color: var(--white);
  padding: 0;
  overflow-y: auto;
  max-height: 200px;
  border-radius: 10px;
  box-shadow: 0px 4px 4px var(--shadow);
  font-family: 'Montserrat', sans-serif;
  padding: 20px 0px;
}

.dropdown {
  scrollbar-width: auto;
  scrollbar-color: var(--background3) var(--light-gray);
}

.dropdown::-webkit-scrollbar {
  width: 8px;
}

.dropdown::-webkit-scrollbar-track {
  background: var(--light-gray);
}

.dropdown::-webkit-scrollbar-thumb {
  background: var(--background3);
  border-radius: 4px;
}

.dropdown::-webkit-scrollbar-thumb:hover {
  background: var(--background2);
}

.dropdown li {
  height: 50px;
  padding-left: 30px;
  display: flex;
  align-items: center;
}

.dropdown li:hover {
  background: var(--background);
  cursor: pointer;
}

.tag {
  position: absolute;
  top: 50%;
  left: 80px;
  transform: translateY(-50%);
  background: var(--black);
  color: var(--white);
  height: 40px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  gap: 20px;
  padding-left: 20px;
  padding-right: 10px;
  font-family: 'Montserrat', sans-serif;
}

.close-icon {
  cursor: pointer;
  height: 17px;
  width: 17px;
}

</style>
