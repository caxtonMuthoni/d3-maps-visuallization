<script setup>
import { RouterView } from 'vue-router'
</script>

<template>
  <nav class="flex items-center h-16 border-b px-5 gap-32">
    <h4 class="font-bold text-lg text-gray-800">Data Visualization</h4>
    <div class="flex gap-4">
      <RouterLink to="/" class="text-gray-500 hover:text-blue-500 transition-all"
        >Recent OSR Trend</RouterLink
      >
      <RouterLink to="/financing" class="text-gray-500 hover:text-blue-500 transition-all"
        >County Financing</RouterLink
      >
      <RouterLink to="/revenue" class="text-gray-500 hover:text-blue-500 transition-all"
        >Revenue deviation</RouterLink
      >
    </div>
  </nav>
  <RouterView />
</template>
