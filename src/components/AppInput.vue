<template>
  <div class="wrapper">

    <img class="icon-left" src="../assets/icons/logo2.svg" />

    <div v-if="selectedUser" class="tag">
      {{ selectedUser.name }}
      <span class="close" @click="clear">×</span>
    </div>

    <input
      v-model="query"
      @input="getUsers"
      :disabled="!!selectedUser"
      :placeholder="selectedUser ? '' : 'Введите запрос'"
    />

    <img v-if="!selectedUser" class="icon-right" src="../assets/icons/search.svg" />

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
    username?: string
    email?: string
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

async function getUsers() {
    if (!query.value) {
        list.value = []
        return
    }

    try {
        const q = encodeURIComponent(query.value)
        const res = await fetch(`https://apimocker.com/users/search?q=${q}`)
        
        if (!res.ok) {
            list.value = []
            return
        }

        const data = await res.json()
        const results = data?.results ?? []
        list.value = results.map((r: any) => ({
            id: r.id,
            name: r.name,
            username: r.username,
            email: r.email
        }))
    } catch (err) {
        console.error('error', err)
        list.value = []
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

.wrapper {
  position: relative;
  width: calc(90vw - 800px);
  min-width: 250px;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  background: #242424;
  border-radius: 10px;
  padding: 0 30px;
}

input {
  width: 100%;
  height: 80px;
  color: white;
  background: #242424;
  padding-left: 30px;
  font-size: 15px;
  border: none;
  outline: black;
}

.icon-left {
  width: 40px;
  height: 40px;
}

.icon-right {
  width: 40px;
  height: 40px;
}

.dropdown {
  position: absolute;
  top: 100px;
  width: calc(90vw - 740px);
  min-width: 350px;
  background: #383836;
  color: white;
  padding: 0;
  overflow-y: auto;
  max-height: 200px;
  border-radius: 10px;
  box-shadow: 0px 4px 4px rgba(0, 0, 0, 0.25);
  font-family: 'Montserrat', sans-serif;
  padding: 20px 0px;
}

.dropdown li {
  height: 50px;
  padding-left: 30px;
  display: flex;
  align-items: center;
}

.dropdown li:hover {
  background: #242424;
  cursor: pointer;
}

.tag {
  position: absolute;
  top: 50%;
  left: 80px;
  transform: translateY(-50%);
  background: black;
  color: white;
  height: 40px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  gap: 20px;
  padding: 0px 20px;
  font-family: 'Montserrat', sans-serif;
}

.close {
  cursor: pointer;
}

</style>
