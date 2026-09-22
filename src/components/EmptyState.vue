<template>
  <div class="empty fade-in">
    <div class="empty-icon" aria-hidden="true">
      <i class="fa-solid fa-cloud-sun"></i>
    </div>
    <h2 class="empty-title">Explore the weather</h2>
    <p class="empty-sub">
      Search for a city or use your current location to get started.
    </p>
    <div class="empty-actions">
      <button class="btn btn-primary" type="button" @click="$emit('focus-search')">
        <i class="fa-solid fa-magnifying-glass" aria-hidden="true"></i>
        <span>Search a city</span>
      </button>
      <button class="btn btn-secondary" type="button" @click="$emit('use-location')">
        <i class="fa-solid fa-location-crosshairs" aria-hidden="true"></i>
        <span>Use my location</span>
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'EmptyState',
  emits: ['focus-search', 'use-location']
}
</script>

<style scoped>
.empty {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  padding: 56px 24px;
  max-width: 520px;
  margin: 0 auto;
}
.empty-icon {
  width: 96px;
  height: 96px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2.6rem;
  color: var(--primary);
  background: linear-gradient(135deg, rgba(56, 189, 248, 0.18), rgba(37, 99, 235, 0.12));
  margin-bottom: 20px;
  animation: float 5s ease-in-out infinite;
  filter: drop-shadow(0 10px 20px rgba(37, 99, 235, 0.2));
}
.empty-title {
  font-size: 1.5rem;
  font-weight: 800;
  color: var(--text);
  letter-spacing: -0.01em;
}
.empty-sub {
  margin-top: 10px;
  color: var(--text-2);
  font-size: 1rem;
  line-height: 1.5;
  max-width: 380px;
}
.empty-actions {
  display: flex;
  gap: 12px;
  margin-top: 28px;
  flex-wrap: wrap;
  justify-content: center;
}
.btn {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 12px 22px;
  border-radius: var(--radius-pill);
  font-weight: 600;
  font-size: 0.9rem;
}
.btn-primary {
  background: linear-gradient(135deg, var(--primary), var(--primary-dark));
  color: #fff;
  box-shadow: 0 6px 14px rgba(37, 99, 235, 0.3);
}
.btn-primary:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 20px rgba(37, 99, 235, 0.35);
}
.btn-secondary {
  background: var(--white);
  color: var(--text);
  border: 1px solid var(--border);
  box-shadow: var(--shadow-sm);
}
.btn-secondary:hover {
  transform: translateY(-2px);
  border-color: var(--sky);
  color: var(--primary);
}

@media (max-width: 480px) {
  .empty-actions { flex-direction: column; width: 100%; }
  .btn { width: 100%; justify-content: center; }
}
</style>