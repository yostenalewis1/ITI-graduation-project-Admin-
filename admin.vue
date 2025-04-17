<template>
  <div class="flex">
    <AdminSidebar />
    <main class="flex-1 p-6 bg-white min-h-screen">
      <slot />
    </main>
  </div>
</template>

<script setup>
import AdminSidebar from '~/components/AdminSidebar.vue'
</script>

