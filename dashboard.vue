 <!-- <template>

  <div class="p-6 space-y-8 text-[#26103d] mt-40">
  <h1 class="text-3xl font-bold text-center">Admin Dashboard</h1>

  <div class="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-4 gap-6">
    <div class="bg-gradient-to-br from-purple-100 to-white rounded-2xl p-6 shadow-lg hover:shadow-xl transition-all text-center">
      <p class="text-sm text-[#26103d] mb-1 ">Total Orders</p>
      <h2 class="text-3xl font-bold text-purple-900">123</h2>
    </div>

    <div class="bg-gradient-to-br from-purple-100 to-white rounded-2xl p-6 shadow-lg hover:shadow-xl transition-all text-center">
      <p class="text-sm text-[#26103d] mb-1">Revenue</p>
      <h2 class="text-3xl font-bold text-purple-900">$4,520</h2>
    </div>

    <div class="bg-gradient-to-br from-purple-100 to-white rounded-2xl p-6 shadow-lg hover:shadow-xl transition-all text-center">
      <p class="text-sm text-[#26103d] mb-1">Users</p>
      <h2 class="text-3xl font-bold text-purple-900">98</h2>
    </div>

    <div class="bg-gradient-to-br from-purple-100 to-white rounded-2xl p-6 shadow-lg hover:shadow-xl transition-all text-center">
      <p class="text-sm text-[#26103d]mb-1">Top Product</p>
      <h2 class="text-3xl font-bold text-purple-900">Red Roses</h2>
    </div>
  </div>
</div>
</template> 


<script setup>
import StatCard from '~/components/StatCard.vue'  // Import the StatCard component
definePageMeta({ layout: 'admin' })
</script> -->




<template>
  <div class="p-6 space-y-8 text-[#26103d] mt-30">
    <h1 class="text-3xl font-bold text-center">Admin Dashboard</h1>
<div class="bg-gradient-to-br from-purple-100 to-white rounded-2xl p-6 shadow-lg hover:shadow-xl transition-all text-center">
  <p class="text-sm text-[#26103d] mb-1">Total Orders</p>
  <h2 class="text-3xl font-bold text-purple-900">{{ totalOrders }}</h2>
</div>

<div class="bg-gradient-to-br from-purple-100 to-white rounded-2xl p-6 shadow-lg hover:shadow-xl transition-all text-center">
  <p class="text-sm text-[#26103d] mb-1">Revenue</p>
  <h2 class="text-3xl font-bold text-purple-900">${{ totalRevenue.toFixed(2) }}</h2>
</div>

<div class="bg-gradient-to-br from-purple-100 to-white rounded-2xl p-6 shadow-lg hover:shadow-xl transition-all text-center">
  <p class="text-sm text-[#26103d] mb-1">Users</p>
  <h2 class="text-3xl font-bold text-purple-900">{{ totalUsers }}</h2>
</div>
</div>
  
</template>
<script setup>
import { ref, onMounted } from 'vue'
import StatCard from '~/components/StatCard.vue'
definePageMeta({ layout: 'admin' })

const totalUsers = ref(0)
const totalOrders = ref(0)
const totalRevenue = ref(0)

// async function fetchDashboardStats() {
//   try {
//     const [usersRes, ordersRes] = await Promise.all([
//       $fetch('https://flower-shop-db.vercel.app/api/v1/users'),
//       $fetch('https://flower-shop-db.vercel.app/api/v1/order')
//     ])

//     totalUsers.value = usersRes.length
//     totalOrders.value = ordersRes.length

//     // Calculate total revenue by summing order totalPrice
//     totalRevenue.value = ordersRes.reduce((sum, order) => sum + order.totalPrice, 0)

//   } catch (error) {
//     console.error('Failed to fetch dashboard stats', error)
//   }
// }

onMounted(() => {
  fetchDashboardStats()
})

async function fetchDashboardStats() {
  try {
    const [usersRes, ordersRes] = await Promise.all([
      $fetch('https://flower-shop-db.vercel.app/api/v1/users', {
        headers: {
          Authorization: `Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI2N2VmYjdmYzRlYTcxOWE0YjRhMjI1ZmUiLCJyb2wiOiJBRE1JTiIsImlhdCI6MTc0Mzc2MzYzNX0.7038QvnRA64VQZLlWgiAOVrDdH2xZd00_xyQNu8OUcY`
        }
      }),
      $fetch('https://flower-shop-db.vercel.app/api/v1/order', {
        headers: {
          Authorization: `Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI2N2VmNzhjMjg0NjllNTVkMzRlNWYyNjEiLCJyb2wiOiJVU0VSIiwiaWF0IjoxNzQzNzU4MzY4fQ.acHGJpBvYgxyfxH6q4JDTcRKx4Udn8vLFmkXBN7T69k`
        }
      })
    ])

    console.log('Users Response:', usersRes)
    console.log('Orders Response:', ordersRes)

    totalUsers.value = usersRes.length
    totalOrders.value = ordersRes.length
    totalRevenue.value = ordersRes.reduce((sum, order) => sum + order.totalPrice, 0)

  } catch (error) {
    console.error('Failed to fetch dashboard stats', error)
  }
}

</script>

