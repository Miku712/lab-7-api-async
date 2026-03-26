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
  <div>
    <h1>Каталог</h1>

    <div v-if="isLoading">Завантаження...</div>
    <div v-else-if="error">Помилка: {{ error }}</div>

    <ul v-else>
      <li v-for="item in items" :key="item.id">
        {{ item.title }}
      </li>
    </ul>

    <div>
      <button @click="prevPage" :disabled="page === 1">
        Попередня
      </button>

      <span> Сторінка {{ page }} </span>

      <button @click="nextPage" :disabled="!hasNextPage">
        Наступна
      </button>
    </div>
  </div>
</template>