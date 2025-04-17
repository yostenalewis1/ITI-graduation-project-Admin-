<!-- <template>
  <div class="p-4 border rounded shadow text-center">
    <h2 class="text-lg text-purple-600">{{ title }}</h2>
    <p class="text-2xl font-bold">{{ value }}</p>
  </div>
</template>

<script setup>
defineProps(['title', 'value'])
</script> -->


<template>
  <div class="p-5 bg-white rounded-2xl shadow-md hover:shadow-lg transition-all duration-300 flex flex-col items-center justify-center text-center space-y-2">
    <div class="bg-purple-100 text-purple-600 w-12 h-12 flex items-center justify-center rounded-full">
      <slot  />
    </div>
    <h2 class="text-md text-purple-500">{{ title }}</h2>
    <p class="text-3xl font-bold text-purple-800">{{ value }}</p>
  </div>
</template>

<script setup>
defineProps(['title', 'value'])
</script>
