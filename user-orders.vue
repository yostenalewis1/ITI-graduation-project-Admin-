<template>
  <div class="p-6 text-purple-900">
    <h1 class="text-2xl font-bold mb-4">User Orders</h1>

    <div v-if="userOrders" class="bg-white shadow rounded-md p-4">
      <h2 class="text-lg font-semibold mb-2">Orders for {{ userName }}</h2>
      <table class="min-w-full text-sm text-left border">
        <thead class="bg-gray-100">
          <tr>
            <th class="px-4 py-2">Order ID</th>
            <th class="px-4 py-2">Date</th>
            <th class="px-4 py-2">Total</th>
            <th class="px-4 py-2">Status</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="order in userOrders"
            :key="order.id"
            class="border-t hover:bg-gray-50"
          >
            <td class="px-4 py-2">{{ order.id }}</td>
            <td class="px-4 py-2">{{ order.date }}</td>
            <td class="px-4 py-2">${{ order.total.toFixed(2) }}</td>
            <td class="px-4 py-2 capitalize">{{ order.status }}</td>
          </tr>
        </tbody>
      </table>
    </div>

    <div v-else class="text-gray-500 mt-4">No orders found for this user.</div>
  </div>
</template>

<script setup>
import { useRoute } from 'vue-router'

const route = useRoute()
const userId = route.query.id

// You can replace this with an API call based on userId
const mockOrders = {
  1: [
    { id: 101, date: '2025-04-01', total: 49.99, status: 'delivered' },
    { id: 102, date: '2025-04-10', total: 29.99, status: 'shipped' },
  ],
  2: [
    { id: 201, date: '2025-03-25', total: 19.99, status: 'canceled' },
  ],
}

const userMap = {
  1: "Jane Doe",
  2: "John Smith",
}

const userOrders = mockOrders[userId] || null
const userName = userMap[userId] || 'Unknown User'

definePageMeta({ layout: 'admin' })
</script>
