<template>
    <div>
      <button class="btn btn-success" @click="showSuccess">Show Success</button>
      <button class="btn btn-danger" @click="showError">Show Error</button>
      <button class="btn btn-warning" @click="showWarning">Show Warning</button>
  
      <BaseNotification ref="notificationRef">
        <!-- Optional: Custom template for notifications -->
        <template #default="{ notification }">
          <div class="text-sm">
            {{ notification.message }}
          </div>
        </template>
      </BaseNotification>
    </div>
  </template>
  <script setup lang="ts">
  import { ref } from 'vue';
  import BaseNotification from '../components/BaseNotification.vue';
  
  const notificationRef = ref();
  
  const showSuccess = () => {
    notificationRef.value?.addNotification({
      type: 'success',
      title: 'Success!',
      message: 'Operation completed successfully',
      duration: 3000,
    });
  };
  
  const showError = () => {
    notificationRef.value?.addNotification({
      type: 'error',
      title: 'Error',
      message: 'Something went wrong',
      duration: 5000,
    });
  };
  
  const showWarning = () => {
    notificationRef.value?.addNotification({
      type: 'warning',
      title: 'Warning',
      message: 'Please check your input',
      duration: 4000,
    });
  };
  </script>