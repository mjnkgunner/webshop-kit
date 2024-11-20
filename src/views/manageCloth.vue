<template>
    <div class="container py-5">
      <!-- Header with Add Button -->
      <div class="d-flex justify-content-between align-items-center mb-4">
        <h2 class="text-gradient text-primary">Manage Products</h2>
        <button class="btn bg-gradient-primary" @click="openModal()">
          Add New Product
        </button>
      </div>
  
      <!-- Loading State -->
      <div v-if="loading" class="text-center">
        <div class="spinner-border text-primary" role="status">
          <span class="visually-hidden">Loading...</span>
        </div>
      </div>
  
      <!-- Error Alert -->
      <div v-if="error" class="alert alert-danger" role="alert">
        {{ error }}
      </div>
  
      <!-- Products Table -->
      <div class="card">
        <div class="table-responsive">
          <table class="table align-items-center mb-0">
            <thead>
              <tr>
                <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">Name</th>
                <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7 ps-2">Category</th>
                <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7 ps-2">Price</th>
                <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7 ps-2">Stock</th>
                <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7 ps-2">Actions</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="product in products" :key="product._id">
                <td>
                  <div class="d-flex px-3 py-1">
                    <div class="d-flex flex-column justify-content-center">
                      <h6 class="mb-0 text-sm">{{ product.name }}</h6>
                    </div>
                  </div>
                </td>
                <td>
                  <p class="text-sm font-weight-bold mb-0">{{ product.category }}</p>
                </td>
                <td>
                  <p class="text-sm font-weight-bold mb-0">${{ product.price }}</p>
                </td>
                <td>
                  <p class="text-sm font-weight-bold mb-0">{{ product.stock }}</p>
                </td>
                <td>
                  <button class="btn btn-link text-secondary mb-0 me-2" @click="openModal(product)">
                    <i class="fas fa-edit text-xs"></i> Edit
                  </button>
                  <button class="btn btn-danger btn-sm mb-0" @click="deleteProduct(product._id)">
                    <i class="fas fa-trash text-xs me-1"></i> Delete
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
  
      <!-- Modal for Add/Edit -->
      <div class="modal fade" id="productModal" tabindex="-1" ref="modal">
        <div class="modal-dialog modal-dialog-centered">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title">{{ isEditing ? 'Edit Product' : 'Add New Product' }}</h5>
              <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body">
              <form @submit.prevent="saveProduct">
                <div class="mb-3">
                  <label class="form-label">Name</label>
                  <input v-model="formData.name" type="text" class="form-control" required>
                </div>
                <div class="mb-3">
                  <label class="form-label">Description</label>
                  <textarea v-model="formData.description" class="form-control" rows="3" required></textarea>
                </div>
                <div class="row">
                  <div class="col-md-6 mb-3">
                    <label class="form-label">Price</label>
                    <input v-model="formData.price" type="number" step="0.01" class="form-control" required>
                  </div>
                  <div class="col-md-6 mb-3">
                    <label class="form-label">Stock</label>
                    <input v-model="formData.stock" type="number" class="form-control" required>
                  </div>
                </div>
                <div class="row">
                  <div class="col-md-4 mb-3">
                    <label class="form-label">Size</label>
                    <input v-model="formData.size" type="text" class="form-control" required>
                  </div>
                  <div class="col-md-4 mb-3">
                    <label class="form-label">Color</label>
                    <input v-model="formData.color" type="text" class="form-control" required>
                  </div>
                  <div class="col-md-4 mb-3">
                    <label class="form-label">Category</label>
                    <input v-model="formData.category" type="text" class="form-control" required>
                  </div>
                </div>
                <div class="modal-footer">
                  <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
                  <button type="submit" class="btn bg-gradient-primary">Save</button>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import axios from 'axios';
  import { Modal } from 'bootstrap';
  
  const products = ref([]);
  const loading = ref(false);
  const error = ref(null);
  let modalInstance = null;
  const isEditing = ref(false);
  const formData = ref({
    name: '',
    description: '',
    price: '',
    size: '',
    color: '',
    category: '',
    stock: ''
  });
  
  // Initialize modal
  onMounted(() => {
    const modalElement = document.getElementById('productModal');
    if (modalElement) {
      modalInstance = new Modal(modalElement);
    }
    fetchProducts();
  });
  
  // Fetch all products
  const fetchProducts = async () => {
    loading.value = true;
    try {
      const response = await axios.get('http://localhost:3000/products');
      products.value = response.data;
    } catch (err) {
      error.value = 'Error loading products. Please try again later.';
      console.error('Error fetching products:', err);
    } finally {
      loading.value = false;
    }
  };
  
  // Open modal for add/edit
  const openModal = (product) => {
    isEditing.value = !!product;
    if (product) {
      formData.value = { ...product };
    } else {
      formData.value = {
        name: '',
        description: '',
        price: '',
        size: '',
        color: '',
        category: '',
        stock: ''
      };
    }
    if (modalInstance) {
      modalInstance.show();
    }
  };
  
  // Save product (create or update)
  const saveProduct = async () => {
    try {
      if (isEditing.value) {
        await axios.put(`http://localhost:3000/products/${formData.value._id}`, formData.value);
      } else {
        await axios.post('http://localhost:3000/products', formData.value);
      }
      await fetchProducts();
      modalInstance.hide();
    } catch (err) {
      error.value = 'Error saving product. Please try again later.';
      console.error('Error saving product:', err);
    }
  };
  
  // Delete product
  const deleteProduct = async (id) => {
    if (!confirm('Are you sure you want to delete this product?')) return;
    
    try {
      await axios.delete(`http://localhost:3000/products/${id}`);
      await fetchProducts();
    } catch (err) {
      error.value = 'Error deleting product. Please try again later.';
      console.error('Error deleting product:', err);
    }
  };
  </script>
  
  <style scoped>
  .card {
    border: none;
    box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.15);
  }
  
  .table > :not(caption) > * > * {
    padding: 1rem 1rem;
  }
  
  .btn-link {
    text-decoration: none;
  }
  </style>