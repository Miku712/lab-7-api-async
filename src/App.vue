<script setup>
import { ref, onMounted } from 'vue'

const items = ref([])
const isLoading = ref(false)
const error = ref(null)

async function loadItems() {
  try {
    isLoading.value = true
    error.value = null

    const response = await fetch('https://jsonplaceholder.typicode.com/posts')

    if (!response.ok) {
      throw new Error('Помилка завантаження')
    }

    const data = await response.json()
    items.value = data.slice(0, 10) 
  } catch (err) {
    error.value = err.message
  } finally {
    isLoading.value = false
  }
}
onMounted(loadItems)
</script>

<template>
  <div class="app">
    <h1>Каталог даних</h1>

    <div v-if="isLoading">Завантаження...</div>
    <div v-else-if="error">Помилка: {{ error }}</div>
    <div v-else-if="items.length === 0">Немає даних</div>

    <ul v-else>
      <li v-for="item in items" :key="item.id">
        {{ item.title }}
      </li>
    </ul>
  </div>
</template>