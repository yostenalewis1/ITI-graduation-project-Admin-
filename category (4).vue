<script setup>
import { useRouter } from "vue-router";
import LoadingIndicator from "/components/LoadingIndicator.vue";
import ErrorMessageIndicator from "/components/ErrorMessageIndicator.vue";
import EmptyIndicator from "/components/EmptyIndicator.vue";
import { useAsyncFetch } from "/composables/useAsyncFetch";
import heart from "../assets/Vector (1).svg";
import fillHeart from "../assets/Fill-heart.svg";
import { useCart } from '../composables/useCart'; 
import { useWishlist } from '../composables/useWishlist'; 
// Create a consistent authorization header object
const getAuthHeaders = () => {
  return {
    authorization: "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI2N2Y0YzI5NjNmMTMwYjliNDkyMTQ1OTIiLCJlbWFpbCI6ImhvZGEubmFkeS45NkBnbWFpbC5jb20iLCJyb2wiOiJBRE1JTiIsImlhdCI6MTc0NDIzNDI0N30.BI9o98RU3DG1cud6Ie8xqdflnTjlUVMIkJgVyWBmhk8"
  };
}











const categories = ref([]);
const selectedFilter = ref("ALL");
const filters = ref(["ALL"]);
const products = ref([]);
const isEmpty = ref(false);

// Admin category management
const showManageCategoriesModal = ref(false);
const showAddCategoryModal = ref(false);
const newCategoryName = ref('');
const newCategoryImage = ref(null);
const editCategoryId = ref(null);
const editCategoryName = ref('');
const editCategoryImage = ref(null);

const { favoriteIds, loading, error, fetchFavorites, addToFavorites, removeFromFavorites } = useWishlist();
const { loadproduct, addToCart } = useCart();

const router = useRouter();

const fetchCategories = async () => {
  loading.value = true;
  error.value = null;
  isEmpty.value = false;

  try {
    const { data, status, message } = await useAsyncFetch(
      "GET",
      "/api/v1/categories/"
    );

    if (status === "success" && data.categories.length > 0) {
      console.log(data);

      categories.value = data.categories.map((item) => ({
        id: item._id,
        name: item.name,
        type: item.name,
        image: item.image,
      }));

      filters.value = ["ALL", ...data.categories.map((item) => item.name)];
      isEmpty.value = false;
    } else {
      isEmpty.value = true;
    }
  } catch (err) {
    error.value = err.message || "Failed to fetch categories.";
  } finally {
    loading.value = false;
  }
};

const fetchProducts = async (categoryId) => {
  loading.value = true;
  error.value = null;
  isEmpty.value = false;

  try {
    const { data, status, message } = await useAsyncFetch(
      "GET",
      `/api/v1/categories/${categoryId}/products`
    );

    if (status === "success" && data.products.length > 0) {
      products.value = data.products;
      isEmpty.value = false;
      products.value = data.products.map((product) => ({
        ...product,
        isFavorite: favoriteIds.value.includes(product._id),
      }));

    } else {
      isEmpty.value = true;
    }
  } catch (err) {
    error.value = err.message || "Failed to fetch products.";
  } finally {
    loading.value = false;
  }
};

onMounted(async () => {
  await fetchFavorites();
  fetchCategories();
});

const filteredCategories = computed(() => {
  return selectedFilter.value === "ALL"
    ? categories.value
    : categories.value.filter((c) => c.type === selectedFilter.value);
});

const toggleFavorite = async (product) => {
  if (product.isFavorite) {
    await removeFromFavorites(product._id);
    product.isFavorite = false;
  } else {
    await addToFavorites(product);
    product.isFavorite = true;
  }
};

const handleFilterClick = (category) => {
  console.log(category);

  selectedFilter.value = category;
  if (category !== "ALL") {
    const selectedCategory = categories.value.find((c) => c.name === category);
    console.log(selectedCategory);

    if (selectedCategory) {
      fetchProducts(selectedCategory.id);
    }
  } else {
    products.value = [];
  }
};

// Admin category management functions
const openManageCategories = () => {
  showManageCategoriesModal.value = true;
};

const closeManageCategories = () => {
  showManageCategoriesModal.value = false;
};

const openAddCategory = () => {
  showAddCategoryModal.value = true;
  showManageCategoriesModal.value = false;
  newCategoryName.value = '';
  newCategoryImage.value = null;
};

const closeAddCategory = () => {
  showAddCategoryModal.value = false;
  showManageCategoriesModal.value = true;
};

const handleImageUpload = (event) => {
  const file = event.target.files[0];
  if (file) {
    newCategoryImage.value = file;
  }
};

const handleEditImageUpload = (event) => {
  const file = event.target.files[0];
  if (file) {
    editCategoryImage.value = file;
  }
};

// const addCategory = async () => {
//   if (!newCategoryName.value || !newCategoryImage.value) {
//     alert('Please provide both category name and image');
//     return;
//   }

//   loading.value = true;
//   error.value = null;

//   try {
//     const formData = new FormData();
//     formData.append('name', newCategoryName.value);
//     formData.append('image', newCategoryImage.value);

//     const { data, status, message } = await useAsyncFetch(
//       "POST",
//       "https://flower-shop-db.vercel.app/api/v1/categories/addCategory",
//       {
//         body: formData,
//         headers: {
//           authorization: "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI2N2Y0YzI5NjNmMTMwYjliNDkyMTQ1OTIiLCJlbWFpbCI6ImhvZGEubmFkeS45NkBnbWFpbC5jb20iLCJyb2wiOiJBRE1JTiIsImlhdCI6MTc0NDIzNDI0N30.BI9o98RU3DG1cud6Ie8xqdflnTjlUVMIkJgVyWBmhk8"
//         }
//       }
//     );

//     if (status === "success") {
//       alert('Category added successfully');
//       closeAddCategory();
//       fetchCategories();
//     } else {
//       alert(`Failed to add category: ${message}`);
//     }
//   } catch (err) {
//     error.value = err.message || "Failed to add category.";
//     alert(`Error: ${error.value}`);
//   } finally {
//     loading.value = false;
//   }
// };
const addCategory = async () => {
  if (!newCategoryName.value || !newCategoryImage.value) {
    alert('Please provide both category name and image');
    return;
  }

  loading.value = true;
  error.value = null;

  try {
    const formData = new FormData();
    formData.append('name', newCategoryName.value);
    formData.append('image', newCategoryImage.value);

    // Use native fetch instead of useAsyncFetch
    const response = await fetch(
      "https://flower-shop-db.vercel.app/api/v1/categories/addCategory",
      {
        method: 'POST',
        body: formData,
        headers: getAuthHeaders() // Use your getAuthHeaders function
      }
    );

    if (!response.ok) {
      const errorData = await response.json().catch(() => ({}));
      throw new Error(errorData.message || `HTTP error! Status: ${response.status}`);
    }

    const data = await response.json();
    
    alert('Category added successfully');
    closeAddCategory();
    fetchCategories();
  } catch (err) {
    console.error('Add category error:', err);
    error.value = err.message || "Failed to add category.";
    alert(`Error: ${error.value}`);
  } finally {
    loading.value = false;
  }
};

const openEditCategory = (category) => {
  editCategoryId.value = category.id;
  editCategoryName.value = category.name;
  editCategoryImage.value = null;
};

// const updateCategory = async () => {
//   if (!editCategoryName.value) {
//     alert('Please provide a category name');
//     return;
//   }

//   loading.value = true;
//   error.value = null;

//   try {
//     const formData = new FormData();
//     formData.append('name', editCategoryName.value);
//     if (editCategoryImage.value) {
//       formData.append('image', editCategoryImage.value);
//     }

//     const { data, status, message } = await useAsyncFetch(
//       "PUT",
//       `https://flower-shop-db.vercel.app/api/v1/categories/${editCategoryId.value}`,
//       {
//         body: formData,
//         headers: {
//           authorization: "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI2N2Y0YzI5NjNmMTMwYjliNDkyMTQ1OTIiLCJlbWFpbCI6ImhvZGEubmFkeS45NkBnbWFpbC5jb20iLCJyb2wiOiJBRE1JTiIsImlhdCI6MTc0NDIzNDI0N30.BI9o98RU3DG1cud6Ie8xqdflnTjlUVMIkJgVyWBmhk8"
//         }
//       }
//     );

//     if (status === "success") {
//       alert('Category updated successfully');
//       editCategoryId.value = null;
//       editCategoryName.value = '';
//       editCategoryImage.value = null;
//       fetchCategories();
//     } else {
//       alert(`Failed to update category: ${message}`);
//     }
//   } catch (err) {
//     error.value = err.message || "Failed to update category.";
//     alert(`Error: ${error.value}`);
//   } finally {
//     loading.value = false;
//   }
// };
// Make sure your useAsyncFetch function properly handles different content types
// const updateCategory = async () => {
//   if (!editCategoryName.value) {
//     alert('Please provide a category name');
//     return;
//   }

//   loading.value = true;
//   error.value = null;

//   try {
//     console.log('Updating category ID:', editCategoryId.value);
//     console.log('New name:', editCategoryName.value);
//     console.log('New image:', editCategoryImage.value);

//     const formData = new FormData();
//     formData.append('name', editCategoryName.value);
//     if (editCategoryImage.value) {
//       formData.append('image', editCategoryImage.value);
//     }
//     console.log('Response status:', response.status);
//       console.log('Response:', data);


//     // Use a direct fetch instead of useAsyncFetch to debug the issue
//     const response = await fetch(
//       `https://flower-shop-db.vercel.app/api/v1/categories/${editCategoryId.value}`,
//       {
//         method: 'PUT',
//         body: formData,
//         headers: getAuthHeaders()
//       }

      
//     );

//     const data = await response.json();
    
//     if (response.ok) {
//       alert('Category updated successfully');
//       editCategoryId.value = null;
//       editCategoryName.value = '';
//       editCategoryImage.value = null;
//       fetchCategories();
//     } else {
//       console.error('API Error:', data);
//       alert(`Failed to update category: ${data.message || response.statusText}`);
//     }
//   } catch (err) {
//     console.error('Fetch Error:', err);
//     error.value = err.message || "Failed to update category.";
//     alert(`Error: ${error.value}`);
//   } finally {
//     loading.value = false;
//   }
// };
const updateCategory = async () => {
  if (!editCategoryName.value) {
    alert('Please provide a category name');
    return;
  }

  loading.value = true;
  error.value = null;

  try {
    console.log('Updating category ID:', editCategoryId.value);
    console.log('New name:', editCategoryName.value);
    
    const formData = new FormData();
    formData.append('name', editCategoryName.value);
    if (editCategoryImage.value) {
      formData.append('image', editCategoryImage.value);
    }

    // Skip Content-Type header when using FormData
    const authHeader = {
      authorization: "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI2N2Y0YzI5NjNmMTMwYjliNDkyMTQ1OTIiLCJlbWFpbCI6ImhvZGEubmFkeS45NkBnbWFpbC5jb20iLCJyb2wiOiJBRE1JTiIsImlhdCI6MTc0NDIzNDI0N30.BI9o98RU3DG1cud6Ie8xqdflnTjlUVMIkJgVyWBmhk8"
    };

    // Use native fetch for testing to bypass any issues with useAsyncFetch
    const response = await fetch(
      `https://flower-shop-db.vercel.app/api/v1/categories/${editCategoryId.value}`,
      {
        method: 'PUT',
        body: formData,
        headers: authHeader
      }
    );

    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }

    const responseData = await response.json();
    
    alert('Category updated successfully');
    editCategoryId.value = null;
    editCategoryName.value = '';
    editCategoryImage.value = null;
    fetchCategories();
  } catch (err) {
    console.error('Update error:', err);
    error.value = err.message || "Failed to update category.";
    alert(`Error: ${error.value}`);
  } finally {
    loading.value = false;
  }
};




// const deleteCategory = async (categoryId) => {
//   if (confirm('Are you sure you want to delete this category?')) {
//     loading.value = true;
//     error.value = null;

//     try {
//       const { data, status, message } = await useAsyncFetch(
//         "DELETE",
//         `https://flower-shop-db.vercel.app/api/v1/categories/${categoryId}`,
//         {
//           headers: {
//             authorization: "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI2N2Y0YzI5NjNmMTMwYjliNDkyMTQ1OTIiLCJlbWFpbCI6ImhvZGEubmFkeS45NkBnbWFpbC5jb20iLCJyb2wiOiJBRE1JTiIsImlhdCI6MTc0NDIzNDI0N30.BI9o98RU3DG1cud6Ie8xqdflnTjlUVMIkJgVyWBmhk8"
//           }
//         }
//       );

//       if (status === "success") {
//         alert('Category deleted successfully');
//         fetchCategories();
//       } else {
//         alert(`Failed to delete category: ${message}`);
//       }
//     } catch (err) {
//       error.value = err.message || "Failed to delete category.";
//       alert(`Error: ${error.value}`);
//     } finally {
//       loading.value = false;
//     }
//   }
// };

const deleteCategory = async (categoryId) => {
  if (confirm('Are you sure you want to delete this category?')) {
    loading.value = true;
    error.value = null;

    try {
      // Use native fetch instead of useAsyncFetch
      const response = await fetch(
        `https://flower-shop-db.vercel.app/api/v1/categories/${categoryId}`,
        {
          method: 'DELETE',
          headers: getAuthHeaders() // Use your auth headers function
        }
      );

      if (!response.ok) {
        const errorData = await response.json().catch(() => ({}));
        throw new Error(errorData.message || `HTTP error! Status: ${response.status}`);
      }

      const data = await response.json();
      
      alert('Category deleted successfully');
      fetchCategories();
    } catch (err) {
      console.error('Delete category error:', err);
      error.value = err.message || "Failed to delete category.";
      alert(`Error: ${error.value}`);
    } finally {
      loading.value = false;
    }
  }
};

const saveManageCategories = () => {
  closeManageCategories();
  fetchCategories();
};

definePageMeta({ layout: 'admin' })
</script>

<template>
  <!-- Show only the loader when loading -->
  <div v-if="loading" class="flex justify-center items-center min-h-screen">
    <LoadingIndicator />
  </div>

  <!-- Main content -->
  <div v-else class="mt-24 ">
    <!-- Heading -->
    <h1 class="text-center text-4xl font-semibold text-[#26103d] mb-6">
      CATEGORIES
    </h1>
    
    <!-- Admin Edit Button -->
    <div class="flex justify-center mb-6">
      <button 
        @click="openManageCategories" 
        class="bg-purple-900 text-white px-6 py-2 rounded-md hover:bg-purple-950 transition-colors">
        Manage Categories
      </button>
    </div>

    <!-- Filters -->
    <div
      class="flex flex-col sm:flex-row justify-center items-center gap-4 sm:gap-12 mb-10 text-lg text-[#26103d]"
    >
      <button
        v-for="category in filters"
        :key="category.id"
        @click="handleFilterClick(category)"
        :class="[
          'font-bold',
          selectedFilter === category ? 'underline' : '',
          'transition-colors duration-300 hover:text-purple-700',
        ]"
      >
        {{ category }}
      </button>
    </div>

    <!-- Error -->
    <div v-if="error">
      <ErrorMessageIndicator :message="error" />
    </div>

    <!-- Empty -->
    <div v-else-if="isEmpty">
      <EmptyIndicator />
    </div>

    <!-- Category Cards -->
    <div v-else class="flex flex-wrap px-10 gap-5 justify-center items-center pb-5 text-[#26103d]">
      <div
        v-if="selectedFilter === 'ALL'"
        v-for="item in filteredCategories"
        :key="item.name"
        class="flex flex-col w-[20%] text-center cursor-pointer"
      >
        <div
          @click="handleFilterClick(item.name)"
          class="p-3 rounded-full border-2"
        >
          <img
            :src="item.image"
            alt="category image"
            class="rounded-full w-[100%] h-fit object-cover mx-auto"
          />
        </div>
        <p class="mt-2 text-lg font-bold text-indigo-950">{{ item.name }}</p>
      </div>
    </div>

    <!-- Product Cards -->
    <div v-if="products.length > 0" class="flex flex-row flex-wrap gap-3 justify-center my-4 pb-5">
      <div
        v-for="(product, index) in products"
        :key="product._id"
        class="flex flex-col w-[350px] pb-5 rounded-xl border-2 gap-4"
      >
        <img
          :src="product.image"
          alt="Bouquet"
          class="rounded-t-xl w-full h-fit object-cover p-5"
        />
        <p class="text-indigo-950 text-sm pl-5">{{ product.title }}</p>
        <div class="flex flex-row justify-between px-5">
          <p class="text-indigo-950 text-md font-bold">
            Price : {{ product.price }} LE
          </p>
          <div class="flex gap-4">
            <button @click="toggleFavorite(product)">
              <img
                :src="product.isFavorite ? fillHeart : heart"
                class="w-6 h-6"
              />
            </button>
            <button @click="addToCart(product._id)">
              <img src="../assets/Vector (3).svg" class="w-6 h-6" />
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- Manage Categories Modal -->
  <div v-if="showManageCategoriesModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 text-[#26103d] ">
    <!-- <div class="bg-purple-200 p-6 rounded-lg w-3/4 max-w-3xl"> -->
        <div class="bg-purple-200 p-6 rounded-lg w-3/4 max-w-3xl   shadow-lg overflow-auto max-h-[90vh] ">
      <!-- <div class="flex justify-between items-center mb-6 overflow-auto max-h-[90vh] w-full max-w-md"> -->
      
      <div class=" p-6 rounded-lg w-full max-w-4xl relative flex flex-col align-middle">
          <button @click="showManageProducts = false" class="absolute top-2 right-2 text-lg font-bold">×</button>
          <h2 class="text-xl font-bold text-center mb-4 text-indigo-950">MANAGE PRODUCTS</h2>
          </div>
      <div class="flex justify-center mb-6">
        <button 
          @click="openAddCategory" 
          class="bg-purple-900 text-white px-12 py-2 rounded-md hover:bg-purple-950 transition-colors">
          ADD
        </button>
      </div>
      
      <div class="overflow-x-auto">
        <table class="w-full bg-white">
          <thead>
            <tr>
              <th class="p-3 border text-left">Category ID</th>
              <th class="p-3 border text-left">Category Image</th>
              <th class="p-3 border text-left">Category Name</th>
              <th class="p-3 border text-left">Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="category in categories" :key="category.id">
              <td class="p-3 border">{{ category.id }}</td>
              <td class="p-3 border">
                <img :src="category.image" alt="Category image" class="w-16 h-16 object-cover" />
              </td>
              <td class="p-3 border">{{ category.name }}</td>
              <td class="p-3 border">
                <div v-if="editCategoryId === category.id" class="space-y-2">
                  <input 
                    v-model="editCategoryName" 
                    type="text" 
                    class="w-full p-2 border rounded bg-white text-[#26103d] " 
                    placeholder="Category Name"
                  />
                  <input 
                    type="file" 
                    @change="handleEditImageUpload" 
                    class="w-full p-2 border rounded bg-white text-[#26103d] " 
                    accept="image/*"
                  />
                  <div class="flex space-x-2">
                    <button 
                      @click="updateCategory" 
                      class="bg-purple-900 text-white px-4 py-1 rounded hover:bg-purple-950">
                      Save
                    </button>
                    <button 
                      @click="editCategoryId = null" 
                      class="bg-gray-300 px-4 py-1 rounded hover:bg-gray-400">
                      Cancel
                    </button>
                  </div>
                </div>
                <div v-else class="flex flex-col space-y-2">
                  <button 
                    @click="openEditCategory(category)" 
                    class="bg-purple-900 text-white px-4 py-1 rounded hover:bg-purple-950 w-full">
                    Edit
                  </button>
                  <button 
                    @click="deleteCategory(category.id)" 
                    class="bg-white border border-gray-400 text-gray-700 px-4 py-1 rounded hover:bg-gray-100 w-full">
                    Delete
                  </button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      
      <div class="flex justify-center mt-6">
        <button 
          @click="saveManageCategories" 
          class="bg-purple-900 text-white px-12 py-2 rounded-md hover:bg-purple-950 transition-colors">
          SAVE
        </button>
      </div>
    </div>
  </div>

  <!-- Add Category Modal -->
  <div v-if="showAddCategoryModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
    <div class="bg-purple-200 p-6 rounded-lg w-11/12 max-w-md">
      <div class="flex justify-between items-center mb-6">
        <h2 class="text-2xl font-bold text-purple-900">ADD CATEGORY</h2>
        <button @click="closeAddCategory" class="text-2xl font-bold">×</button>
      </div>
      
      <div class="space-y-4">
        <input 
          v-model="newCategoryName" 
          type="text" 
          class="w-full p-3 rounded border focus:outline-none focus:ring-2 focus:ring-purple-500 bg-white text-[#26103d] " 
          placeholder="Category Name"
        />
        
        <input 
          type="file" 
          @change="handleImageUpload" 
          class="w-full p-3 rounded border focus:outline-none focus:ring-2 focus:ring-purple-500 bg-white text-[#26103d] " 
          accept="image/*"
          placeholder="Category Image"
        />
      </div>
      
      <div class="flex justify-center mt-6">
        <button 
          @click="addCategory" 
          class="bg-purple-900 text-white px-16 py-2 rounded-md hover:bg-purple-950 transition-colors">
          SAVE
        </button>
      </div>
    </div>
  </div>
</template>