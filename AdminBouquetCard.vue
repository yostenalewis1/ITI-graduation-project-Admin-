<template>
    <div v-if="products.length > 0" class="flex flex-row flex-wrap gap-6 mx-auto">
      <div v-for="product in products"
        :key="product._id"
        class="flex flex-col w-[350px] pb-5 rounded-xl border-2 gap-4 cursor-pointer hover:shadow-lg transition-shadow duration-300">
        
        <img :src="product.image || 'https://via.placeholder.com/200x200?text=No+Image'"
          :alt="product.title"
          class="rounded-t-xl w-full h-64 object-cover p-5" />
        
        <p class="text-indigo-950 text-sm pl-5">{{ product.title }}</p>
        
        <div class="flex flex-row justify-between px-5">
          <p class="text-indigo-950 text-md font-bold">Price: {{ product.price }} LE</p>
          
          <div class="flex gap-4">
            <p class="text-indigo-950 text-sm">Stock: {{ product.stock || 1 }}</p>
          </div>
        </div>
      </div>
    </div>
    <div v-else class="w-full text-center py-10 text-purple-950 font-bold">
      loading products...
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  // In AdminBouquetCard.vue
const props = defineProps({
  productsList: {
    type: Array,
    default: () => []
  }
});

// Use props.productsList in your template, or merge with locally fetched data
const products = ref([]);

// Watch for changes to props
watch(() => props.productsList, (newProducts) => {
  if (newProducts && newProducts.length > 0) {
    products.value = newProducts;
  }
}, { immediate: true });
  
  const authToken = "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI2N2Y0YzI5NjNmMTMwYjliNDkyMTQ1OTIiLCJlbWFpbCI6ImhvZGEubmFkeS45NkBnbWFpbC5jb20iLCJyb2wiOiJBRE1JTiIsImlhdCI6MTc0NDIzNDI0N30.BI9o98RU3DG1cud6Ie8xqdflnTjlUVMIkJgVyWBmhk8";

//   const products = ref([]);
  
  onMounted(async () => {
    try {
      const response = await fetch("https://flower-shop-db.vercel.app/api/v1/products/all", {
        method: "GET",
        headers: {
          "authorization": authToken
        }
      });
      
      if (response.ok) {
        const data = await response.json();
        products.value = data.products || [];
      }
    } catch (error) {
      console.error("Failed to fetch products:", error);
    }
  });
  </script>





