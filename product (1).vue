
  <template>
    <div class="w-full top-15 relative flex flex-col gap-10 px-10 mb-56">


      <div class="flex justify-between items-center">



        <p class="text-2xl font-cairo font-bold text-purple-950 w-full text-left">BOUQUET:</p>
        <button 
          @click="openManageProducts" 
          class="bg-purple-900 text-white px-6 py-2 rounded-md hover:bg-purple-950 transition-colors">
          Edit
        </button>
      </div>
      
      <div class="flex flex-row flex-wrap gap-6">
        <AdminBouquetCard :productsList="products" />
      </div>
      
      
      <div v-if="showManageProducts" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 text-purple-950">
        <div class="bg-[#8C6E82] p-6 rounded-lg w-full max-w-4xl relative flex flex-col align-middle">
          <button @click="showManageProducts = false" class="absolute top-2 right-2 text-lg font-bold">×</button>
          <h2 class="text-xl font-bold text-center mb-4 text-indigo-950">MANAGE PRODUCTS</h2>
          
          <button 
            @click="openAddProductModal" 
            class="bg-purple-950 text-white px-6 py-2 rounded-md hover:bg-purple-950 transition-colors mb-4 w-full">
            ADD
          </button>
          
          <div class="overflow-x-auto">
            <table class="w-full border-collapse bg-white">
              <thead>
                <tr class="bg-white">
                  <th class="p-2 text-left">ID</th>
                  <th class="p-2 text-left">Product Name</th>
                  <th class="p-2 text-left">Image</th>
                  <th class="p-2 text-center">Description</th>
                  <th class="p-2 text-left">Price</th>
                  <th class="p-2 text-left">Quantity</th>
                  <th class="p-2 text-center">Action</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(product, index) in products" :key="product._id" class="border-t">
                  <td class="p-2">{{ index + 1 }}</td>
                  <td class="p-2">{{ product.title }}</td>
                  <td class="p-2">
                    <img 
                      :src="product.image || 'https://via.placeholder.com/50x50?text=No+Image'" 
                      class="w-10 h-10 object-cover" 
                      :alt="product.title"
                    />
                  </td>
                  <td class="p-2 max-w-xs truncate">{{ product.description || 'No description' }}</td>
                  <td class="p-2">{{ product.price }} LE</td>
                  <td class="p-2">{{ product.stock || 1 }}</td>
                  <td class="p-2 space-x-2">
                    <button 
                      @click="editProduct(product)" 
                      class="bg-purple-950 text-white px-3 py-1 text-sm rounded-md hover:bg-purple-950 transition-colors w-full ml-2 mb-2">
                      Edit
                    </button>
                    <button 
                      @click="confirmDeleteProduct(product._id)" 
                      class="bg-red-500 text-white px-3 py-1 text-sm rounded-md hover:bg-red-600 transition-colors w-full">
                      Delete
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
          
          <button 
            @click="saveChanges" 
            class="bg-purple-950 text-white px-6 py-2 rounded-md hover:bg-purple-950 transition-colors mt-4 w-full">
            SAVE
          </button>
        </div>
      </div>
      
      
      <div v-if="showAddProductModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
        <div class="bg-pink-100 p-6 rounded-lg w-full max-w-md relative">
          <button @click="closeAddProductModal" class="absolute top-2 right-2 text-lg font-bold text-purple-950">×</button>
          <h2 class="text-xl font-bold text-center mb-6 text-indigo-950">
            {{ isEditing ? 'EDIT PRODUCT' : 'ADD PRODUCT' }}
          </h2>
          
          <form @submit.prevent="submitProduct" class="space-y-4">
            <div>
              <input 
                v-model="productForm.title" 
                type="text" 
                placeholder="Product Name" 
                class="w-full p-2 rounded border focus:outline-none focus:ring-2 focus:ring-purple-500 bg-white text-purple-950"
                required
              />
            </div>
            
            <div>
              <input 
                v-model="productForm.description" 
                type="text" 
                placeholder="Description" 
                class="w-full p-2 rounded border focus:outline-none focus:ring-2 focus:ring-purple-500 bg-white text-purple-950"
                required
              />
            </div>
            
            <div>
              <input 
                v-model="productForm.price" 
                type="number" 
                step="0.01" 
                placeholder="Price" 
                class="w-full p-2 rounded border focus:outline-none focus:ring-2 focus:ring-purple-500 bg-white text-purple-950"
                required
              />
            </div>
            
            <div>
              <input 
                v-model="productForm.stock" 
                type="number" 
                placeholder="Quantity" 
                class="w-full p-2 rounded border focus:outline-none focus:ring-2 focus:ring-purple-500 bg-white text-purple-950"
                required
              />
            </div>
            
            <div>
              <input 
                v-model="productForm.priceAfterDiscount" 
                type="number" 
                step="0.01" 
                placeholder="Price After Discount" 
                class="w-full p-2 rounded border focus:outline-none focus:ring-2 focus:ring-purple-500 bg-white text-purple-950"
              />
            </div>
            
            <div>
              <select 
                v-model="productForm.category" 
                class="w-full p-2 rounded border focus:outline-none focus:ring-2 focus:ring-purple-500 bg-white text-purple-950"
              >
                <option value="" disabled>Select Category</option>
                <option v-for="category in categories" :key="category._id" :value="category._id">
                  {{ category.name }}
                </option>
              </select>
            </div>
            
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-1">Product Image</label>
              <input 
                ref="imageInput"
                type="file" 
                @change="handleImageUpload"
                class="w-full p-2 rounded border focus:outline-none focus:ring-2 focus:ring-purple-500 bg-white text-purple-950"
              />
            </div>
            
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-1">Cover Image (Optional)</label>
              <input 
                ref="coverImageInput"
                type="file" 
                @change="handleCoverImageUpload"
                class="w-full p-2 rounded border focus:outline-none focus:ring-2 focus:ring-purple-500 bg-white text-purple-950"
              />
            </div>
            
            <button 
              type="submit" 
              class="bg-purple-900 text-white px-6 py-2 rounded-md hover:bg-purple-950 transition-colors w-full">
              SAVE
            </button>
          </form>
        </div>
      </div>
      
      
      <div v-if="showDeleteConfirmation" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 text-purple-950">
        <div class="bg-white p-6 rounded-lg w-full max-w-md">
          <h2 class="text-xl font-bold mb-4">Confirm Delete</h2>
          <p>Are you sure you want to delete this product? This action cannot be undone.</p>
          <div class="flex justify-end space-x-2 mt-6">
            <button 
              @click="showDeleteConfirmation = false" 
              class="px-4 py-2 bg-gray-200 rounded-md hover:bg-gray-300 transition-colors text-purple-950">
              Cancel
            </button>
            <button 
              @click="deleteProduct" 
              class="bg-purple-900 text-white px-6 py-2 rounded-md hover:bg-purple-950 transition-colors">
              Delete
            </button>
          </div>
        </div>
      </div>
    </div>
  </template>
  
 <script setup>
  import { ref, onMounted } from 'vue';
  import AdminBouquetCard from '~/components/AdminBouquetCard.vue';
  
  
  // Auth token
  const authToken = "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI2N2Y0YzI5NjNmMTMwYjliNDkyMTQ1OTIiLCJlbWFpbCI6ImhvZGEubmFkeS45NkBnbWFpbC5jb20iLCJyb2wiOiJBRE1JTiIsImlhdCI6MTc0NDIzNDI0N30.BI9o98RU3DG1cud6Ie8xqdflnTjlUVMIkJgVyWBmhk8";
  
  // State variables
  const products = ref([]);
  const categories = ref([]);
  const showManageProducts = ref(false);
  const showAddProductModal = ref(false);
  const showDeleteConfirmation = ref(false);
  const isEditing = ref(false);
  const productToDeleteId = ref(null);
  
  // Image file references
  const imageInput = ref(null);
  const coverImageInput = ref(null);
  
  // Form data
  const productForm = ref({
    title: '',
    description: '',
    price: '',
    stock: '',
    category: '',
    priceAfterDiscount: '',
    image: null,
    coverImage: null,
    _id: null
  });
  
  // Fetch products from API
  const fetchProducts = async () => {
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
      } else {
        console.error("Failed to fetch products:", await response.text());
      }
    } catch (error) {
      console.error("Error fetching products:", error);
    }
  };
  
  // Fetch categories from API
  const fetchCategories = async () => {
    try {
      const response = await fetch("https://flower-shop-db.vercel.app/api/v1/categories", {
        method: "GET",
        headers: {
          "authorization": authToken
        }
      });
      
      if (response.ok) {
        const data = await response.json();
        categories.value = data.categories || [];
      } else {
        console.error("Failed to fetch categories:", await response.text());
      }
    } catch (error) {
      console.error("Error fetching categories:", error);
    }
  };
  
  // Submit product form
  const submitProduct = async () => {
    try {
      const formData = new FormData();
      formData.append('title', productForm.value.title);
      formData.append('description', productForm.value.description);
      formData.append('price', productForm.value.price);
      formData.append('stock', productForm.value.stock);
      
      if (productForm.value.category) {
        formData.append('category', productForm.value.category);
      }
      
      if (productForm.value.priceAfterDiscount) {
        formData.append('priceAfterDiscount', productForm.value.priceAfterDiscount);
      } else {
        formData.append('priceAfterDiscount', productForm.value.price);
      }
      
      if (productForm.value.image) {
        formData.append('image', productForm.value.image);
      }
      
      if (productForm.value.coverImage) {
        formData.append('coverImage', productForm.value.coverImage);
      }
      
      let url = "https://flower-shop-db.vercel.app/api/v1/products/addProduct";
      let method = "POST";
      
      if (isEditing.value && productForm.value._id) {
        url = `https://flower-shop-db.vercel.app/api/v1/products/${productForm.value._id}`;
        method = "PUT";
      }
      
      const response = await fetch(url, {
        method: method,
        headers: {
          "authorization": authToken
        },
        body: formData
      });
      
      if (response.ok) {
        await fetchProducts();
        closeAddProductModal();
        alert("Product saved successfully!");
      } else {
        const errorData = await response.json();
        
        if (errorData.message && errorData.message.includes("duplicate key error")) {
          alert("A product with this title already exists. Please use a different title.");
        } else {
          alert("Failed to save product: " + (errorData.message || "Unknown error"));
        }
        console.error("Failed to save product:", errorData);
      }
    } catch (error) {
      console.error("Error saving product:", error);
      alert("Error saving product: " + error.message);
    }
  };
  
  // Open manage products modal
  const openManageProducts = async () => {
    await fetchProducts();
    showManageProducts.value = true;
  };
  
  // Open add product modal
  const openAddProductModal = () => {
    resetForm();
    showManageProducts.value = false;
    showAddProductModal.value = true;
  };
  
  // Close add/edit product modal
  const closeAddProductModal = () => {
    showAddProductModal.value = false;
    showManageProducts.value = true;
    resetForm();
  };
  
  // Reset form
  const resetForm = () => {
    productForm.value = {
      title: '',
      description: '',
      price: '',
      stock: '',
      category: '',
      priceAfterDiscount: '',
      image: null,
      coverImage: null,
      _id: null
    };
    isEditing.value = false;
    
    // Reset file inputs
    if (imageInput.value) imageInput.value.value = '';
    if (coverImageInput.value) coverImageInput.value.value = '';
  };
  
  // Edit product
  const editProduct = (product) => {
    isEditing.value = true;
    productForm.value = {
      _id: product._id,
      title: product.title,
      description: product.description || '',
      price: product.price,
      stock: product.stock || 1,
      category: product.category || categories.value[0]?._id || '',
      priceAfterDiscount: product.priceAfterDiscount || product.price,
      image: null,
      coverImage: null
    };
    
    showManageProducts.value = false;
    showAddProductModal.value = true;
  };
  
  // Handle image upload
  const handleImageUpload = (event) => {
    productForm.value.image = event.target.files[0];
  };
  
  // Handle cover image upload
  const handleCoverImageUpload = (event) => {
    productForm.value.coverImage = event.target.files[0];
  };
  
  // Confirm product deletion
  const confirmDeleteProduct = (productId) => {
    productToDeleteId.value = productId;
    showDeleteConfirmation.value = true;
  };
  
  // Delete product
  const deleteProduct = async () => {
    try {
      const response = await fetch(`https://flower-shop-db.vercel.app/api/v1/products/${productToDeleteId.value}`, {
        method: "DELETE",
        headers: {
          "authorization": authToken
        }
      });
      
      if (response.ok) {
        products.value = products.value.filter(product => product._id !== productToDeleteId.value);
        showDeleteConfirmation.value = false;
      } else {
        console.error("Failed to delete product");
        alert("Failed to delete product");
      }
    } catch (error) {
      console.error("Error deleting product:", error);
      alert("Error deleting product: " + error.message);
    }
  };
  
  // Save changes (close manage products modal)
  const saveChanges = () => {
    showManageProducts.value = false;
  };
  
  // Initialize data on component mount
  onMounted(async () => {
    await fetchProducts();
    await fetchCategories();
  });
  
  definePageMeta({ layout: 'admin' });
  </script>









  
  

  
  