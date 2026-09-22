<template>
  <div class="loading-wrap fade-in" role="status" aria-live="polite">
    <div class="spinner" aria-hidden="true">
      <i class="fa-solid fa-cloud-sun"></i>
    </div>
    <p class="loading-text">{{ message }}</p>
    <p class="loading-sub">Please wait a moment.</p>
  </div>
</template>

<script>
export default {
  name: 'LoadingMessage',
  props: {
    message: { type: String, default: 'Loading...' }
  }
}
</script>

<style scoped>
.loading-wrap {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 64px 20px;
  gap: 14px;
}
.spinner {
  position: relative;
  width: 64px;
  height: 64px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  background: rgba(37, 99, 235, 0.08);
  color: var(--primary);
  font-size: 1.6rem;
}
.spinner::after {
  content: '';
  position: absolute;
  inset: -4px;
  border-radius: 50%;
  border: 3px solid transparent;
  border-top-color: var(--primary);
  border-right-color: var(--sky);
  animation: spin 0.9s linear infinite;
}
.loading-text {
  font-weight: 700;
  color: var(--text);
  font-size: 1.05rem;
}
.loading-sub {
  color: var(--text-2);
  font-size: 0.9rem;
}
</style>