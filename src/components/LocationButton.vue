<template>
  <button
    class="location-btn"
    type="button"
    :class="{ 'is-active': active, 'is-loading': loading }"
    :aria-busy="loading"
    aria-label="Use my current location"
    @click="$emit('click')"
  >
    <i
      class="icon"
      :class="loading ? 'fa-solid fa-spinner fa-spin' : 'fa-solid fa-location-crosshairs'"
      aria-hidden="true"
    ></i>
    <span class="label">
      <template v-if="loading">Detecting location...</template>
      <template v-else-if="active">Location detected</template>
      <template v-else>Use My Location</template>
    </span>
  </button>
</template>

<script>
export default {
  name: 'LocationButton',
  props: {
    loading: { type: Boolean, default: false },
    active: { type: Boolean, default: false }
  },
  emits: ['click']
}
</script>

<style scoped>
.location-btn {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  padding: 12px 22px;
  border-radius: var(--radius-pill);
  background: var(--white);
  color: var(--text);
  font-weight: 600;
  font-size: 0.9rem;
  border: 2px solid transparent;
  box-shadow: var(--shadow-sm);
  background-image:
    linear-gradient(var(--white), var(--white)),
    linear-gradient(135deg, var(--sky), var(--primary));
  background-origin: border-box;
  background-clip: padding-box, border-box;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}
.location-btn:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-md);
}
.location-btn.is-active {
  background: linear-gradient(135deg, rgba(16, 185, 129, 0.12), rgba(52, 211, 153, 0.12));
  color: #047857;
  border-color: var(--success);
}
.location-btn.is-loading { cursor: progress; }
.icon { font-size: 1rem; color: var(--primary); }
.location-btn.is-active .icon { color: var(--success); }

@media (max-width: 640px) {
  .location-btn { width: 100%; justify-content: center; }
}
</style>