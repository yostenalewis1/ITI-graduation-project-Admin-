<!-- <template>
  <div class="p-6 text-purple-900">
    <h1 class="text-2xl font-bold mb-4 ">Manage Users</h1>

    <div class="overflow-x-auto bg-white shadow rounded-md">
      <table class="min-w-full text-sm text-left">
        <thead class="bg-gray-100">
          <tr>
            <th class="px-4 py-2">Name</th>
            <th class="px-4 py-2">Email</th>
            <th class="px-4 py-2">Status</th>
            <th class="px-4 py-2">Orders</th>
            <th class="px-4 py-2">Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="user in users" 
            :key="user.id"
            class="border-t hover:bg-gray-50"
          >
            <td class="px-4 py-2">{{ user.name }}</td>
            <td class="px-4 py-2">{{ user.email }}</td>
            <td class="px-4 py-2 capitalize">{{ user.status }}</td>
            <td class="px-4 py-2">{{ user.orders }}</td>
            <td class="px-4 py-2 space-x-2">
              <button
                class="bg-blue-500 hover:bg-blue-600 text-white px-3 py-1 rounded"
                @click="viewOrders(user.id)"
              >
                View Orders
              </button>
              <button
                :class="user.status === 'active' ? 'bg-red-500 hover:bg-red-600' : 'bg-green-500 hover:bg-green-600'"
                class="text-white px-3 py-1 rounded"
                @click="toggleStatus(user)"
              >
                {{ user.status === 'active' ? 'Deactivate' : 'Reactivate' }}
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
const users = ref([
  {
    id: 1,
    name: "Jane Doe",
    email: "jane@example.com",
    status: "active",
    orders: 12,
  },
  {
    id: 2,
    name: "John Smith",
    email: "john@example.com",
    status: "inactive",
    orders: 5,
  },
]);

function toggleStatus(user) {
  user.status = user.status === "active" ? "inactive" : "active";
  // Optionally make an API call here to update status
}

function viewOrders(userId) {
  // Navigate to user's order history
  // Example: /admin/user-orders?id=1
  navigateTo(`/admin/user-orders?id=${userId}`);

}

definePageMeta({ layout: 'admin' })
</script> -->





<!-- <template>
  <div class="p-6 text-purple-900">
    <h1 class="text-2xl font-bold mb-4">Manage Users</h1>



    <div class="overflow-x-auto bg-white shadow rounded-md">
      <table class="min-w-full text-sm text-left">
        <thead class="bg-gray-100">
          <tr>
            <th class="px-4 py-2">Name</th>
            <th class="px-4 py-2">Email</th>
            <th class="px-4 py-2">Status</th>
            <th class="px-4 py-2">Orders</th>
            <th class="px-4 py-2">Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="user in users"
            :key="user.id"
            class="border-t hover:bg-gray-50"
          >
            <td class="px-4 py-2">{{ user.name }}</td>
            <td class="px-4 py-2">{{ user.email }}</td>
            <td class="px-4 py-2 capitalize">{{ user.status }}</td>
            <td class="px-4 py-2">{{ user.orders || 0 }}</td>
            <td class="px-4 py-2 space-x-2">
              <button
                class="bg-blue-500 hover:bg-blue-600 text-white px-3 py-1 rounded"
                @click="viewOrders(user.id)"
              >
                View Orders
              </button>
              <button
                :class="user.status === 'active' ? 'bg-red-500 hover:bg-red-600' : 'bg-green-500 hover:bg-green-600'"
                class="text-white px-3 py-1 rounded"
                @click="toggleStatus(user)"
              >
                {{ user.status === 'active' ? 'Deactivate' : 'Reactivate' }}
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
const users = ref([])

const { data: usersData, refresh, error } = useAsyncData('users', async () => {
  const res = await $fetch('https://flower-shop-db.vercel.app/api/v1/users/user')
  return res.users || []  // adjust if needed
})

// Update `users` when data is ready
watchEffect(() => {
  if (usersData.value) {
    users.value = usersData.value
  }
})

async function toggleStatus(user) {
  const newStatus = user.status === "active" ? "inactive" : "active"
  try {
    await $fetch(`https://flower-shop-db.vercel.app/api/v1/users/${user.id}`, {
      method: 'PATCH',
      body: { status: newStatus }
    })
    user.status = newStatus
    // Or await refresh() to reload from backend if needed
  } catch (err) {
    console.error("Failed to update status:", err)
  }
}

function viewOrders(userId) {
  navigateTo(`/admin/user-orders?id=${userId}`)
}

definePageMeta({ layout: 'admin' })
</script> -->








<template>
  <div class="p-6 text-purple-900">
    <h1 class="text-2xl font-bold mb-4">Manage Users</h1>

    <div v-if="pending" class="text-center py-4">Loading...</div> <!-- Show a loading indicator -->
    <div v-else class="overflow-x-auto bg-white shadow rounded-md">
      <table class="min-w-full text-sm text-left">
        <thead class="bg-gray-100">
          <tr>
            <th class="px-4 py-2">Name</th>
            <th class="px-4 py-2">Email</th>
            <th class="px-4 py-2">Status</th>
            <th class="px-4 py-2">Orders</th>
            <th class="px-4 py-2">Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="user in users"
            :key="user.id"
            class="border-t hover:bg-gray-50"
          >
            <td class="px-4 py-2">{{ user.name }}</td>
            <td class="px-4 py-2">{{ user.email }}</td>
            <td class="px-4 py-2 capitalize">{{ user.status }}</td>
            <td class="px-4 py-2">{{ user.orders || 0 }}</td>
            <td class="px-4 py-2 space-x-2">
              <button
                class="bg-blue-500 hover:bg-blue-600 text-white px-3 py-1 rounded"
                @click="viewOrders(user.id)"
              >
                View Orders
              </button>
              <button
                :class="user.status === 'active' ? 'bg-red-500 hover:bg-red-600' : 'bg-green-500 hover:bg-green-600'"
                class="text-white px-3 py-1 rounded"
                @click="toggleStatus(user)"
              >
                {{ user.status === 'active' ? 'Deactivate' : 'Reactivate' }}
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
const users = ref([])
const pending = ref(true)  // Add a pending state to track loading

// const { data: usersData, refresh, error } = useAsyncData('users', async () => {
//   const res = await $fetch('https://flower-shop-db.vercel.app/api/v1/users/user')
//   return res.users || []  // Adjust the response based on your API
// })
const { data: usersData, refresh, error } = useAsyncData('users', async () => {
  const token = useCookie('auth_token')?.value;  // Example of getting token from cookies
  const res = await $fetch('https://flower-shop-db.vercel.app/api/v1/users/user', {
    headers: {
      authorization: `Bearer ${token}`  // Include the token in the headers
    }
  });
  return res.users || [];
});

// Update `users` when data is ready
watchEffect(() => {
  if (usersData.value) {
    users.value = usersData.value
    pending.value = false  // Set pending to false when data is loaded
  }

  if (error.value) {
    pending.value = false  // Set pending to false in case of an error
    console.error("Failed to fetch users:", error.value)  // Log the error
  }
})

async function toggleStatus(user) {
  const newStatus = user.status === "active" ? "inactive" : "active"
  try {
    await $fetch(`https://flower-shop-db.vercel.app/api/v1/users/${user.id}`, {
      method: 'PATCH',
      body: { status: newStatus }
    })
    user.status = newStatus
    // Or await refresh() to reload from backend if needed
  } catch (err) {
    console.error("Failed to update status:", err)
  }
}

function viewOrders(userId) {
  navigateTo(`/admin/user-orders?id=${userId}`)
}

definePageMeta({ layout: 'admin' })
</script>















