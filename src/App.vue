<script setup>
import { ref, onMounted } from 'vue'

const items = ref([])
const page = ref(1)
const limit = 5

const isLoading = ref(false)
const error = ref(null)
const hasNextPage = ref(true)

let controller = null

async function loadItems() {
  try {
    if (controller) {
      controller.abort()
    }

    controller = new AbortController()

    isLoading.value = true
    error.value = null

    const response = await fetch(
      `https://jsonplaceholder.typicode.com/posts?_page=${page.value}&_limit=${limit}`,
      { signal: controller.signal }
    )

    if (!response.ok) {
      throw new Error('Помилка завантаження')
    }

    const data = await response.json()

    items.value = data
    hasNextPage.value = data.length === limit

  } catch (err) {
    if (err.name !== 'AbortError') {
      error.value = err.message
    }
  } finally {
    isLoading.value = false
  }
}

function nextPage() {
  if (hasNextPage.value) {
    page.value++
    loadItems()
  }
}

function prevPage() {
  if (page.value > 1) {
    page.value--
    loadItems()
  }
}

onMounted(loadItems)
</script>

<template>
  <div class="app">
    <h1>Каталог</h1>

    <div v-if="isLoading">Завантаження...</div>
    <div v-else-if="error">Помилка: {{ error }}</div>

    <ul v-else class="list">
      <li v-for="item in items" :key="item.id" class="card">
        {{ item.title }}
      </li>
    </ul>

    <div class="pagination">
      <button @click="prevPage" :disabled="page === 1">
        ⬅
      </button>

      <span>Сторінка {{ page }}</span>

      <button @click="nextPage" :disabled="!hasNextPage">
        ➡
      </button>
    </div>
  </div>
</template>

<style scoped>
.app {
  max-width: 700px;
  margin: auto;
  padding: 20px;
  font-family: 'Segoe UI', sans-serif;
}

h1 {
  text-align: center;
  color: #0369a1;
  margin-bottom: 20px;
}

.list {
  list-style: none;
  padding: 0;
}

.card {
  background-color: #e0f2ff;
  padding: 12px;
  margin-bottom: 10px;
  border-radius: 10px;
  color: #075985;
  box-shadow: 0 2px 6px rgba(0,0,0,0.08);
  transition: 0.2s;
}

.card:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0,0,0,0.15);
}

.pagination {
  margin-top: 20px;
  display: flex;
  justify-content: center;
  gap: 15px;
  align-items: center;
}

button {
  padding: 8px 14px;
  background-color: #0284c7;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}

button:hover {
  background-color: #0369a1;
}

button:disabled {
  background-color: #94a3b8;
  cursor: not-allowed;
}
</style>