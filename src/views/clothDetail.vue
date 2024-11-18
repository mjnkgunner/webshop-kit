<template>
  <div class="container py-5">
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

    <!-- Product Detail -->
    <div v-else-if="product" class="row">
      <!-- Product Image -->
      <div class="col-12 col-md-6 mb-4">
        <div class="card">
          <img 
            src='/product.png' 
            :alt="product.name"
            class="card-img-top"
          >
        </div>
      </div>

      <!-- Product Info -->
      <div class="col-12 col-md-6">
        <div class="card">
          <div class="card-body">
            <h2 class="card-title text-gradient text-primary mb-4">
              {{ product.name }}
            </h2>
            
            <div class="mb-4">
              <h3 class="h5">Description</h3>
              <p class="text-muted">{{ product.description }}</p>
            </div>

            <div class="mb-4">
              <h3 class="h5">Price</h3>
              <p class="h4 text-primary">${{ product.price }}</p>
            </div>

            <!-- Additional Details -->
            <div class="mb-4" v-if="product.details">
              <h3 class="h5">Details</h3>
              <ul class="list-unstyled">
                <li v-for="(value, key) in product.details" :key="key">
                  <strong>{{ key }}:</strong> {{ value }}
                </li>
              </ul>
            </div>

            <!-- Action Buttons -->
            <div class="d-flex gap-3">
              <button 
                class="btn bg-gradient-primary"
                @click="addToCart"
              >
                Add to Cart
              </button>
              <button 
                class="btn btn-outline-secondary"
                @click="goBack"
              >
                Back to List
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Not Found State -->
    <div v-else class="text-center">
      <div class="alert alert-warning" role="alert">
        Product not found
      </div>
      <button 
        class="btn btn-outline-primary mt-3"
        @click="goBack"
      >
        Back to List
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import axios from 'axios';

const route = useRoute();
const router = useRouter();
const product = ref(null);
const loading = ref(false);
const error = ref(null);

const getProduct = async () => {
  loading.value = true;
  error.value = null;

  try {
    const response = await axios.get(
      `http://localhost:3000/products/${route.params.id}`
    );
    product.value = response.data;
  } catch (err) {
    if (err.response?.status === 404) {
      product.value = null;
    } else {
      error.value = 'Error loading product details. Please try again later.';
      console.error('Error fetching product:', err);
    }
  } finally {
    loading.value = false;
  }
};

const addToCart = () => {
  // Implement add to cart functionality
  console.log('Adding to cart:', product.value);
  // You can emit an event or call a store action here
};

const goBack = () => {
  router.push({ name: 'homepage' });
};

onMounted(() => {
  getProduct();
});
</script>

<style scoped>
.card {
  border: none;
  box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.15);
}

.card-img-top {
  object-fit: cover;
  height: 400px;
}

@media (max-width: 768px) {
  .card-img-top {
    height: 300px;
  }
}
</style>
  