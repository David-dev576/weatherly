<template>
  <div class="stat">
    <div class="stat-icon" aria-hidden="true">
      <i :class="icon"></i>
    </div>
    <div class="stat-body">
      <p class="stat-label">{{ label }}</p>
      <p class="stat-value">{{ value }}</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'WeatherStat',
  props: {
    icon: { type: String, default: 'fa-solid fa-circle-info' },
    label: { type: String, required: true },
    value: { type: [String, Number], required: true }
  }
}
</script>

<style scoped>
.stat {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 14px 16px;
  background: rgba(255, 255, 255, 0.7);
  border: 1px solid var(--border);
  border-radius: var(--radius);
  backdrop-filter: blur(6px);
  transition: transform 0.2s ease, box-shadow 0.2s ease, border-color 0.2s ease;
}
.stat:hover {
  transform: translateY(-3px);
  border-color: var(--sky);
  box-shadow: var(--shadow-md);
}
.stat-icon {
  font-size: 1.15rem;
  width: 42px;
  height: 42px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 12px;
  color: var(--primary);
  background: linear-gradient(135deg, rgba(56, 189, 248, 0.18), rgba(37, 99, 235, 0.12));
}
.stat-label {
  font-size: 0.78rem;
  color: var(--text-2);
  text-transform: uppercase;
  letter-spacing: 0.05em;
  font-weight: 600;
}
.stat-value {
  font-size: 1.05rem;
  font-weight: 700;
  color: var(--text);
  margin-top: 2px;
}
</style>