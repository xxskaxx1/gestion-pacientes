<template>
  <transition name="app-notification">
    <div v-if="show" class="app-notification" :class="type">
      <span class="notification-icon">
        <svg v-if="type === 'success'" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24">
          <path fill="currentColor" d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-2 15l-5-5 1.41-1.41L10 14.17l7.59-7.59L19 8l-9 9z"/>
        </svg>
        <svg v-else-if="type === 'error'" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24">
          <path fill="currentColor" d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm1 15h-2v-2h2v2zm0-4h-2V7h2v6z"/>
        </svg>
        <svg v-else xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24">
          <path fill="currentColor" d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm1 15h-2v-2h2v2zm0-4h-2V7h2v6z"/>
        </svg>
      </span>
      <span class="notification-content">{{ message }}</span>
      <button @click="close" class="close-btn" aria-label="Cerrar notificación">
        &times;
      </button>
    </div>
  </transition>
</template>

<script>
export default {
  name: 'AppNotification',
  props: {
    show: {
      type: Boolean,
      default: false
    },
    message: {
      type: String,
      required: true
    },
    type: {
      type: String,
      default: 'success',
      validator: (value) => ['success', 'error', 'warning', 'info'].includes(value)
    },
    timeout: {
      type: Number,
      default: 5000
    },
    closable: {
      type: Boolean,
      default: true
    }
  },
  watch: {
    show(newVal) {
      if (newVal && this.timeout > 0) {
        setTimeout(() => {
          this.close()
        }, this.timeout)
      }
    }
  },
  methods: {
    close() {
      this.$emit('close')
    }
  }
}
</script>

<style scoped>
.app-notification {
  position: fixed;
  bottom: 20px;
  right: 20px;
  padding: 15px 20px;
  border-radius: 8px;
  color: white;
  display: flex;
  align-items: center;
  gap: 12px;
  max-width: 400px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  z-index: 1000;
  animation: slideIn 0.3s ease-out;
  transition: all 0.3s ease;
}

.app-notification.success {
  background-color: #4CAF50;
  border-left: 5px solid #388E3C;
}

.app-notification.error {
  background-color: #F44336;
  border-left: 5px solid #D32F2F;
}

.app-notification.warning {
  background-color: #FF9800;
  border-left: 5px solid #F57C00;
}

.app-notification.info {
  background-color: #2196F3;
  border-left: 5px solid #1976D2;
}

.notification-icon {
  display: flex;
  align-items: center;
}

.notification-content {
  flex-grow: 1;
  font-size: 14px;
  line-height: 1.5;
}

.close-btn {
  background: none;
  border: none;
  color: white;
  font-size: 20px;
  cursor: pointer;
  margin-left: 10px;
  opacity: 0.7;
  transition: opacity 0.2s;
  padding: 0;
  line-height: 1;
}

.close-btn:hover {
  opacity: 1;
}

.notification-enter-active,
.notification-leave-active {
  transition: all 0.3s ease;
}

.notification-enter-from {
  opacity: 0;
  transform: translateY(20px);
}

.notification-leave-to {
  opacity: 0;
  transform: translateX(100%);
}

@keyframes slideIn {
  from {
    transform: translateX(100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

@media (max-width: 600px) {
  .app-notification {
    left: 20px;
    right: 20px;
    max-width: none;
  }
}
</style>