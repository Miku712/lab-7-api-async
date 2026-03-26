<script setup>
import { ref } from 'vue'

const items = ref([])
const isLoading = ref(false)
const error = ref(null)
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

<style scoped>
.app {
  max-width: 700px;
  margin: auto;
  padding: 20px;
}
h1 {
  color: #0369a1;
}
</style>