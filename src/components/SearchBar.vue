<template>
  <form class="search-bar" role="search" @submit.prevent="onSubmit">
    <label for="city-input" class="sr-only">City name</label>

    <i class="fa-solid fa-magnifying-glass search-icon" aria-hidden="true"></i>

    <input
      id="city-input"
      ref="input"
      :value="modelValue"
      type="text"
      class="search-input"
      placeholder="Search for a city..."
      autocomplete="off"
      aria-label="Search for a city"
      @input="$emit('update:modelValue', $event.target.value)"
    />

    <button
      type="submit"
      class="search-btn"
      :disabled="loading"
      :aria-busy="loading"
    >
      <template v-if="!loading">
        <i class="fa-solid fa-magnifying-glass" aria-hidden="true"></i>
        <span>Search</span>
      </template>
      <template v-else>
        <i class="fa-solid fa-spinner fa-spin" aria-hidden="true"></i>
        <span>Searching...</span>
      </template>
    </button>
  </form>
</template>

<script>
export default {
  name: 'SearchBar',
  props: {
    modelValue: { type: String, default: '' },
    loading: { type: Boolean, default: false }
  },
  emits: ['update:modelValue', 'search'],
  methods: {
    onSubmit() {
      if (this.loading) return
      this.$emit('search')
    },
    focus() {
      this.$refs.input?.focus()
    }
  }
}
</script>

<style scoped>
.search-bar {
  display: flex;
  align-items: center;
  gap: 10px;
  width: 100%;
  max-width: 520px;
  padding: 8px 8px 8px 18px;
  background: var(--white);
  border: 1px solid var(--border);
  border-radius: var(--radius-pill);
  box-shadow: var(--shadow-sm);
  transition: box-shadow 0.25s ease, border-color 0.25s ease, transform 0.25s ease;
}
.search-bar:focus-within {
  border-color: var(--primary);
  box-shadow: 0 0 0 4px rgba(37, 99, 235, 0.15), var(--shadow-md);
  transform: translateY(-1px);
}
.search-icon { color: var(--text-2); font-size: 0.95rem; }

.search-input {
  flex: 1;
  min-width: 0;
  border: none;
  outline: none;
  background: transparent;
  font-size: 0.95rem;
  color: var(--text);
  padding: 10px 4px;
}
.search-input::placeholder { color: #94A3B8; }

.search-btn {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 10px 20px;
  border-radius: var(--radius-pill);
  background: linear-gradient(135deg, var(--primary), var(--primary-dark));
  color: #fff;
  font-weight: 600;
  font-size: 0.9rem;
  box-shadow: 0 6px 14px rgba(37, 99, 235, 0.35);
  white-space: nowrap;
}
.search-btn:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 10px 20px rgba(37, 99, 235, 0.4);
}
.search-btn:disabled { opacity: 0.75; cursor: progress; }

.sr-only {
  position: absolute;
  width: 1px; height: 1px;
  padding: 0; margin: -1px;
  overflow: hidden; clip: rect(0, 0, 0, 0);
  white-space: nowrap; border: 0;
}

@media (max-width: 640px) {
  .search-bar { max-width: 100%; }
  .search-btn { padding: 10px 16px; }
}
</style>