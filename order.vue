

 <template>
  <div class="p-6 text-[#26103d] min-h-screen">
    <h1 class="text-3xl font-bold mb-6 text-[#26103d] ml-20">Manage Orders</h1>

    <select
      v-model="filter"
      class="mb-6 p-2 border rounded w-1/3 bg-white text-[#26103d] text-center"
    >
      <option value="">All</option>
      <option value="pending">Pending</option>
      <option value="accepted">Accepted</option>
      <option value="delivered">Delivered</option>
      <option value="canceled">Canceled</option>
    </select>

    
    <div v-if="pending" class="flex justify-center items-center h-96">
      <LoadingIndicator />
    </div>

  
    <div v-else-if="error" class="flex justify-center items-center h-96">
      <ErrorMessageIndicator message="Error loading orders." />
    </div>

    
    <div v-else-if="filteredOrders.length">
      <div
        v-for="order in filteredOrders"
        :key="order._id"
        class="bg-white shadow-md rounded-2xl p-5 mb-4"
      >
        <div class="flex justify-between items-center">
          <div>
            <p class="text-lg font-semibold">Order ID: {{ order._id }}</p>
            <p class="text-gray-500">User: {{ order.user?.name || 'N/A' }}</p>
            <p class="text-gray-500">Total: ${{ order.total }}</p>
          </div>
          <div class="text-right">
            <span
              class="inline-block px-3 py-1 rounded-full text-sm font-medium"
              :class="{
                'bg-yellow-100 text-yellow-800': order.status === 'pending',
                'bg-green-100 text-green-800': order.status === 'accepted',
                'bg-blue-100 text-blue-800': order.status === 'delivered',
                'bg-red-100 text-red-800': order.status === 'canceled'
              }"
            >
              {{ order.status }}
            </span>
          </div>
        </div>
      </div>
    </div>

  
    <div v-else class="flex justify-center items-center h-96">
      <EmptyIndicator message="No orders found." />
    </div>
  </div>
</template>


<script setup>
definePageMeta({ layout: 'admin' })

import { useAsyncFetch } from '~/composables/useAsyncFetch'
import LoadingIndicator from '~/components/LoadingIndicator.vue'
import ErrorMessageIndicator from '~/components/ErrorMessageIndicator.vue'
import EmptyIndicator from '~/components/EmptyIndicator.vue'

import { ref, computed, onMounted } from 'vue'

const filter = ref('')
const orders = ref([])
const pending = ref(true)
const error = ref(false)

const fetchOrders = async () => {
  pending.value = true
  error.value = false

  try {
    const { data, status, message } = await useAsyncFetch('GET', '/api/v1/order')

    if (status === 'success') {
      orders.value = data.orders || []
    } else {
      console.error('Fetch orders error:', message)
      error.value = true
    }
  } catch (err) {
    console.error('Unexpected error fetching orders:', err)
    error.value = true
  }

  pending.value = false
}

onMounted(fetchOrders)

const filteredOrders = computed(() =>
  filter.value
    ? orders.value.filter(order => order.status === filter.value)
    : orders.value
)
</script>





