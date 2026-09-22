<template>
  <div class="error-wrap fade-in" role="alert">
    <div class="error-icon" aria-hidden="true">
      <i :class="iconClass"></i>
    </div>
    <h3 class="error-title">{{ title }}</h3>
    <p class="error-message">{{ message }}</p>
    <button class="retry-btn" type="button" @click="$emit('retry')">
      <i class="fa-solid fa-rotate-right" aria-hidden="true"></i>
      <span>Try again</span>
    </button>
  </div>
</template>

<script>
export default {
  name: 'ErrorMessage',
  props: {
    type: { type: String, default: 'generic' },
    title: { type: String, default: 'Something went wrong' },
    message: { type: String, default: '' }
  },
  emits: ['retry'],
  computed: {
    iconClass() {
      switch (this.type) {
        case 'not-found':       return 'fa-solid fa-circle-xmark'
        case 'network':         return 'fa-solid fa-triangle-exclamation'
        case 'gps-denied':      return 'fa-solid fa-location-dot'
        case 'gps-unavailable': return 'fa-solid fa-location-dot'
        case 'gps-timeout':     return 'fa-solid fa-clock'
        case 'gps-error':       return 'fa-solid fa-location-dot'
        case 'unsupported':     return 'fa-solid fa-ban'
        case 'api':             return 'fa-solid fa-cloud-bolt'
        default:                return 'fa-solid fa-triangle-exclamation'
      }
    }
  }
}
</script>

<style scoped>
.error-wrap {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  padding: 48px 24px;
  max-width: 480px;
  margin: 0 auto;
  background: var(--white);
  border: 1px solid var(--border);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-sm);
}
.error-icon {
  width: 64px;
  height: 64px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.7rem;
  color: var(--error);
  background: rgba(239, 68, 68, 0.1);
  margin-bottom: 16px;
}
.error-title {
  font-size: 1.2rem;
  font-weight: 700;
  color: var(--text);
  margin-bottom: 8px;
}
.error-message {
  color: var(--text-2);
  font-size: 0.95rem;
  line-height: 1.5;
  margin-bottom: 20px;
}
.retry-btn {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 10px 22px;
  border-radius: var(--radius-pill);
  background: linear-gradient(135deg, var(--primary), var(--primary-dark));
  color: #fff;
  font-weight: 600;
  font-size: 0.9rem;
  box-shadow: 0 6px 14px rgba(37, 99, 235, 0.3);
}
.retry-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 20px rgba(37, 99, 235, 0.35);
}
</style>