<template>
  <Teleport to="body">
    <TransitionGroup
      enter-active-class="fade show"
      leave-active-class="fade"
    >
      <div
        v-for="notification in notifications"
        :key="notification.id"
        class="position-fixed"
        :style="getNotificationPosition(notification.index)"
      >
        <div
          class="alert alert-dismissible d-flex align-items-center"
          :class="getNotificationStyles(notification.type)"
          style="min-width: 320px; max-width: 400px;"
        >
          <div class="d-flex justify-content-between w-100">
            <div>
              <!-- Icon based on type -->
              <i class="bi me-2" :class="getNotificationIcon(notification.type)"></i>
              
              <slot name="header" :notification="notification">
                <strong>{{ notification.title }}</strong>
              </slot>
              
              <div class="mt-1">
                <slot :notification="notification">
                  {{ notification.message }}
                </slot>
              </div>
            </div>

            <button
              @click="removeNotification(notification.id)"
              class="btn-close"
              aria-label="Close"
            > x </button>
          </div>
        </div>
      </div>
    </TransitionGroup>
  </Teleport>
</template>

<script setup lang="ts">
import { ref } from 'vue';

interface NotificationItem {
  id: number;
  type: 'success' | 'error' | 'warning' | 'info';
  title: string;
  message: string;
  duration: number;
  index: number;
}

const notifications = ref<NotificationItem[]>([]);
let notificationId = 0;

const getNotificationStyles = (type: NotificationItem['type']) => {
  const styles = {
    success: 'alert-success',
    error: 'alert-danger',
    warning: 'alert-warning',
    info: 'alert-info',
  };
  
  return styles[type];
};

const getNotificationIcon = (type: NotificationItem['type']) => {
  const icons = {
    success: 'bi-check-circle-fill',
    error: 'bi-exclamation-circle-fill',
    warning: 'bi-exclamation-triangle-fill',
    info: 'bi-info-circle-fill',
  };
  
  return icons[type];
};

const getNotificationPosition = (index: number) => {
  const top = 16 + index * (16 + 80); // 16px spacing, 80px approx height
  return {
    top: `${top}px`,
    right: '16px',
    zIndex: 1050
  };
};

const removeNotification = (id: number) => {
  notifications.value = notifications.value.filter(n => n.id !== id);
  // Update indices after removal
  notifications.value.forEach((notification, index) => {
    notification.index = index;
  });
};

const addNotification = (options: Partial<NotificationItem>) => {
  const notification: NotificationItem = {
    id: notificationId++,
    type: options.type || 'info',
    title: options.title || '',
    message: options.message || '',
    duration: options.duration || 3000,
    index: notifications.value.length,
  };

  notifications.value.push(notification);

  // Auto remove notification after duration
  setTimeout(() => {
    removeNotification(notification.id);
  }, notification.duration);
};

defineExpose({
  addNotification,
  removeNotification,
});
</script>

<style scoped>
.fade {
  transition: opacity 0.15s linear;
}

.fade:not(.show) {
  opacity: 0;
}
</style>
