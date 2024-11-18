<template>
  <div class="container py-5">
    <div class="row">
      <!-- Header -->
      <div class="col-12 mb-4">
        <h2 class="text-gradient text-primary">Product List</h2>
      </div>

      <!-- Product Grid -->
      <div class="col-12">
        <!-- Loading State -->
        <div v-if="loading" class="text-center">
          <div class="spinner-border text-primary" role="status">
            <span class="visually-hidden">Loading...</span>
          </div>
        </div>

        <!-- Error State -->
        <div v-else-if="error" class="alert alert-danger" role="alert">
          {{ error }}
        </div>

        <!-- Product List -->
        <div v-else class="row g-4">
          <div v-for="product in clothes" :key="product.id" 
               class="col-12 col-md-6 col-lg-4">
            <div class="card h-100 cursor-pointer" @click="goToDetail(product._id)">
              <img src="/product.png" class="card-img-top" alt="Product Image">
              <div class="card-body">
                <h5 class="card-title">{{ product.name }}</h5>
                <p class="card-text mb-2">
                  <span class="text-muted">Color:</span> {{ product.color }}
                </p>
                <p class="card-text text-primary font-weight-bold">
                  ${{ product.price }}
                </p>
              </div>
            </div>
          </div>
          <!-- Empty State -->
          <div v-if="clothes.length === 0" class="col-12 text-center">
            <p>No products found</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';
import axios from 'axios';

const router = useRouter();
const clothes = ref([]);
const loading = ref(false);
const error = ref(null);

const getProducts = async () => {
  loading.value = true;
  error.value = null;
  
  try {
    const response = await axios.get('http://localhost:3000/products');
    clothes.value = response.data;
  } catch (err) {
    error.value = 'Error loading products. Please try again later.';
    console.error('Error fetching products:', err);
  } finally {
    loading.value = false;
  }
};

const goToDetail = (id) => {
  router.push({ 
    name: 'clothdetail', 
    params: { id }
  });
};

onMounted(() => {
  getProducts();
});
</script>

<style scoped>
.cursor-pointer {
  cursor: pointer;
  transition: transform 0.2s;
}

.cursor-pointer:hover {
  transform: translateY(-5px);
}
</style>
  