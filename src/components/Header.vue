<template>
  <header class="header">
    <div class="header-inner">
      <div class="brand">
        <span class="logo" aria-hidden="true">
          <i class="fa-solid fa-cloud-sun"></i>
        </span>
        <div class="brand-text">
          <h1 class="brand-name">Weatherly</h1>
          <p class="brand-tag">Your weather, anywhere.</p>
        </div>
      </div>

      <button
        class="gps-btn"
        type="button"
        :class="{ 'is-active': locationActive, 'is-loading': locationLoading }"
        :aria-busy="locationLoading"
        aria-label="Use my current location"
        @click="$emit('use-location')"
      >
        <i
          class="gps-icon"
          :class="locationLoading ? 'fa-solid fa-spinner fa-spin' : 'fa-solid fa-location-dot'"
          aria-hidden="true"
        ></i>
        <span class="gps-label">
          {{ locationLoading ? 'Detecting...' : locationActive ? 'Location active' : 'Use my location' }}
        </span>
      </button>
    </div>
  </header>
</template>

<script>
export default {
  name: 'AppHeader',
  props: {
    locationLoading: { type: Boolean, default: false },
    locationActive: { type: Boolean, default: false }
  },
  emits: ['use-location']
}
</script>

<style scoped>
.header {
  position: sticky;
  top: 0;
  z-index: 20;
  backdrop-filter: blur(12px);
  background: rgba(255, 255, 255, 0.75);
  border-bottom: 1px solid var(--border);
}
.header-inner {
  max-width: 1100px;
  margin: 0 auto;
  padding: 14px 20px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
}
.brand { display: flex; align-items: center; gap: 12px; }

.logo {
  font-size: 1.6rem;
  color: var(--primary);
  filter: drop-shadow(0 4px 10px rgba(37, 99, 235, 0.35));
  animation: float 4s ease-in-out infinite;
  display: inline-flex;
}
.brand-name {
  font-size: 1.15rem;
  font-weight: 800;
  color: var(--text);
  letter-spacing: -0.01em;
}
.brand-tag {
  font-size: 0.75rem;
  color: var(--text-2);
  margin-top: 1px;
}

.gps-btn {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 10px 16px;
  border-radius: var(--radius-pill);
  background: linear-gradient(135deg, var(--primary), var(--sky));
  color: #fff;
  font-weight: 600;
  font-size: 0.85rem;
  box-shadow: var(--shadow-md);
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}
.gps-btn:hover { transform: translateY(-2px); box-shadow: var(--shadow-lg); }
.gps-btn:active { transform: translateY(0); }
.gps-btn.is-active { background: linear-gradient(135deg, var(--success), #34D399); }
.gps-btn.is-loading { cursor: progress; opacity: 0.9; }

@media (max-width: 520px) {
  .brand-tag { display: none; }
  .gps-label { display: none; }
  .gps-btn { padding: 10px 12px; }
}
</style>