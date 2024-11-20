<script setup>
import NavbarDefault from "@/examples/navbars/NavbarDefault.vue";
import FooterCentered from "@/examples/footers/FooterCentered.vue";
import Header from "@/examples/Header.vue";
import ClothList from "@/views/clothList.vue";
</script>

<template>
  <div class="container-fluid px-0">
    <!-- Navbar -->
    <NavbarDefault />
    <!-- Main Content -->
    <Header 
        title="Scam Store"
        image="/public/banner.jpg"
        center
    />
    <main>
      <!-- Content Sections -->
      <section class="py-5">
        <div class="container">
          <div class="row">
            <div class="col-md-8 mx-auto text-center">
              <h2>Welcome to our store</h2>
              <p class="lead">
                We are a store that sells clothes
              </p>
              <router-link to="/manage-products" class="btn btn-primary btn-lg">
                <i class="fas fa-cog me-2"></i>Manage Products
              </router-link>
            </div>
          </div>
          <div class="row">
            <ClothList />
          </div>
        </div>
      </section>
    </main>
    
    <!-- Footer -->
    <FooterCentered />
  </div>
</template>
