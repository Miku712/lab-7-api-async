<script setup>
import { ref, onMounted } from 'vue'

const items = ref([])
const selectedItem = ref(null)
const isLoading = ref(false)
const error = ref(null)

async function loadItems() {
  try {
    isLoading.value = true
    const res = await fetch('https://jsonplaceholder.typicode.com/posts')
    const data = await res.json()
    items.value = data.slice(0, 10)
  } catch (err) {
    error.value = err.message
  } finally {
    isLoading.value = false
  }
}

function selectItem(item) {
  selectedItem.value = item
}

function back() {
  selectedItem.value = null
}

onMounted(loadItems)
</script>

<template>
  <div class="app">
    <h1>Каталог</h1>

    <div v-if="isLoading">Завантаження...</div>
    <div v-else-if="error">Помилка: {{ error }}</div>

    <div v-else-if="selectedItem">
      <h2>{{ selectedItem.title }}</h2>
      <p>{{ selectedItem.body }}</p>
      <button @click="back">Назад</button>
    </div>

    <ul v-else>
      <li v-for="item in items" :key="item.id">
        {{ item.title }}
        <button @click="selectItem(item)">Деталі</button>
      </li>
    </ul>
  </div>
</template>