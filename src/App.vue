<script setup>
import { ref, onMounted, computed } from 'vue'

const items = ref([])
const query = ref('')
const selectedItem = ref(null)
const isLoading = ref(false)
const error = ref(null)

async function loadItems() {
  isLoading.value = true
  const res = await fetch('https://jsonplaceholder.typicode.com/posts')
  items.value = (await res.json()).slice(0, 20)
  isLoading.value = false
}

const filteredItems = computed(() => {
  return items.value.filter(item =>
    item.title.toLowerCase().includes(query.value.toLowerCase())
  )
})

onMounted(loadItems)
</script>

<template>
  <div class="app">
    <h1>Каталог</h1>

    <input v-model="query" placeholder="Пошук..." />

    <div v-if="filteredItems.length === 0">
      Нічого не знайдено
    </div>

    <ul>
      <li v-for="item in filteredItems" :key="item.id">
        {{ item.title }}
      </li>
    </ul>
  </div>
</template>