<template>
  <article class="weather-card fade-in" :class="{ 'is-day': weather.isDay }">
    <!-- HEADER -->
    <header class="card-header">
      <div class="location">
        <i class="fa-solid fa-location-dot pin" aria-hidden="true"></i>
        <div>
          <h2 class="city">{{ city }}</h2>
          <p class="country" v-if="country">{{ country }}</p>
        </div>
      </div>

      <span class="source-badge" :class="locationSource === 'gps' ? 'gps' : 'search'">
        <i
          :class="locationSource === 'gps'
            ? 'fa-solid fa-location-crosshairs'
            : 'fa-solid fa-magnifying-glass'"
          aria-hidden="true"
        ></i>
        <span>{{ locationSource === 'gps' ? 'GPS Location' : 'City Search' }}</span>
      </span>
    </header>

    <!-- DATE -->
    <p class="date">{{ formattedDate }}</p>

    <!-- MAIN TEMP -->
    <div class="main-temp">
      <div class="weather-icon" aria-hidden="true">
        <i :class="weatherIconClass"></i>
      </div>
      <div class="temp-block">
        <p class="temp">{{ weather.temperature }}<span class="unit">°C</span></p>
        <p class="condition">{{ weatherCondition }}</p>
      </div>
    </div>

    <!-- STATS (v-for) -->
    <div class="stats-grid">
      <WeatherStat
        v-for="stat in stats"
        :key="stat.label"
        :icon="stat.icon"
        :label="stat.label"
        :value="stat.value"
      />
    </div>

    <!-- COORDS -->
    <footer class="card-footer">
      <span>Lat: {{ Number(latitude).toFixed(2) }}°</span>
      <span class="dot">•</span>
      <span>Lon: {{ Number(longitude).toFixed(2) }}°</span>
    </footer>
  </article>
</template>

<script>
import WeatherStat from './WeatherStat.vue'

export default {
  name: 'WeatherCard',
  components: { WeatherStat },

  props: {
    weather: { type: Object, required: true },
    city: { type: String, required: true },
    country: { type: String, default: '' },
    latitude: { type: [Number, String], required: true },
    longitude: { type: [Number, String], required: true },
    locationSource: { type: String, default: 'search' }
  },

  computed: {
    formattedDate() {
      const d = new Date()
      return d.toLocaleDateString('en-US', {
        weekday: 'long',
        month: 'long',
        day: 'numeric'
      })
    },

    weatherCondition() {
      return this.getWeatherCondition(this.weather.weatherCode)
    },

    weatherIconClass() {
      return this.getWeatherIconClass(this.weather.weatherCode, this.weather.isDay)
    },

    stats() {
      return [
        { icon: 'fa-solid fa-droplet',              label: 'Humidity',   value: `${this.weather.humidity}%` },
        { icon: 'fa-solid fa-wind',                 label: 'Wind Speed', value: `${this.weather.windSpeed} km/h` },
        { icon: 'fa-solid fa-temperature-half',     label: 'Feels Like', value: `${this.weather.feelsLike}°C` },
        { icon: 'fa-solid fa-gauge-high',           label: 'Pressure',   value: `${this.weather.pressure} hPa` }
      ]
    }
  },

  methods: {
    /* WMO Weather Codes → human readable */
    getWeatherCondition(code) {
      const map = {
        0: 'Clear Sky',
        1: 'Mainly Clear', 2: 'Partly Cloudy', 3: 'Overcast',
        45: 'Fog', 48: 'Depositing Rime Fog',
        51: 'Light Drizzle', 53: 'Moderate Drizzle', 55: 'Dense Drizzle',
        56: 'Light Freezing Drizzle', 57: 'Dense Freezing Drizzle',
        61: 'Slight Rain', 63: 'Moderate Rain', 65: 'Heavy Rain',
        66: 'Light Freezing Rain', 67: 'Heavy Freezing Rain',
        71: 'Slight Snow', 73: 'Moderate Snow', 75: 'Heavy Snow',
        77: 'Snow Grains',
        80: 'Slight Rain Showers', 81: 'Moderate Rain Showers', 82: 'Violent Rain Showers',
        85: 'Slight Snow Showers', 86: 'Heavy Snow Showers',
        95: 'Thunderstorm',
        96: 'Thunderstorm with Slight Hail',
        99: 'Thunderstorm with Heavy Hail'
      }
      return map[code] || 'Unknown'
    },

    /* WMO Weather Codes → Font Awesome class */
    getWeatherIconClass(code, isDay = true) {
      // Clear
      if (code === 0) return isDay ? 'fa-solid fa-sun' : 'fa-solid fa-moon'
      if (code === 1) return isDay ? 'fa-solid fa-cloud-sun' : 'fa-solid fa-cloud-moon'
      if (code === 2) return isDay ? 'fa-solid fa-cloud-sun' : 'fa-solid fa-cloud-moon'
      if (code === 3) return 'fa-solid fa-cloud'
      // Fog
      if (code === 45 || code === 48) return 'fa-solid fa-smog'
      // Drizzle
      if (code >= 51 && code <= 57) return 'fa-solid fa-cloud-rain'
      // Rain
      if (code >= 61 && code <= 67) return 'fa-solid fa-cloud-showers-heavy'
      // Snow
      if (code >= 71 && code <= 77) return 'fa-solid fa-snowflake'
      // Rain showers
      if (code >= 80 && code <= 82) return 'fa-solid fa-cloud-showers-heavy'
      // Snow showers
      if (code === 85 || code === 86) return 'fa-solid fa-snowflake'
      // Thunderstorm
      if (code === 95) return 'fa-solid fa-cloud-bolt'
      if (code === 96 || code === 99) return 'fa-solid fa-cloud-bolt'
      return 'fa-solid fa-temperature-half'
    }
  }
}
</script>

<style scoped>
.weather-card {
  width: 100%;
  max-width: 720px;
  background:
    radial-gradient(600px 300px at 100% 0%, rgba(56, 189, 248, 0.18), transparent 55%),
    radial-gradient(500px 250px at 0% 100%, rgba(37, 99, 235, 0.10), transparent 60%),
    var(--white);
  border: 1px solid var(--border);
  border-radius: var(--radius-lg);
  padding: 32px;
  box-shadow: var(--shadow-lg);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}
.weather-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 30px 60px rgba(37, 99, 235, 0.18);
}

.card-header {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 12px;
}
.location { display: flex; align-items: center; gap: 10px; }
.pin { font-size: 1.1rem; color: var(--primary); }
.city {
  font-size: 1.35rem;
  font-weight: 800;
  color: var(--text);
  letter-spacing: -0.01em;
}
.country {
  font-size: 0.85rem;
  color: var(--text-2);
  margin-top: 2px;
}
.source-badge {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  font-size: 0.75rem;
  font-weight: 700;
  padding: 6px 12px;
  border-radius: var(--radius-pill);
  white-space: nowrap;
}
.source-badge.gps {
  background: rgba(16, 185, 129, 0.12);
  color: #047857;
  border: 1px solid rgba(16, 185, 129, 0.25);
}
.source-badge.search {
  background: rgba(37, 99, 235, 0.1);
  color: var(--primary);
  border: 1px solid rgba(37, 99, 235, 0.2);
}

.date {
  margin-top: 12px;
  color: var(--text-2);
  font-size: 0.9rem;
  font-weight: 500;
}

/* MAIN TEMP */
.main-temp {
  display: flex;
  align-items: center;
  gap: 24px;
  margin: 24px 0 8px;
  padding: 20px 0;
  border-top: 1px solid var(--border);
  border-bottom: 1px solid var(--border);
}
.weather-icon {
  font-size: 4.5rem;
  line-height: 1;
  color: var(--sun);
  animation: float 5s ease-in-out infinite;
  filter: drop-shadow(0 12px 24px rgba(245, 158, 11, 0.35));
}
.temp-block { flex: 1; }
.temp {
  font-size: clamp(3rem, 8vw, 4.5rem);
  font-weight: 800;
  color: var(--text);
  letter-spacing: -0.03em;
  line-height: 1;
}
.unit {
  font-size: 1.6rem;
  font-weight: 600;
  color: var(--text-2);
  margin-left: 4px;
}
.condition {
  margin-top: 8px;
  font-size: 1.05rem;
  font-weight: 600;
  color: var(--primary);
}

/* STATS */
.stats-grid {
  margin-top: 24px;
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 12px;
}

/* FOOTER */
.card-footer {
  margin-top: 20px;
  text-align: center;
  color: var(--text-2);
  font-size: 0.8rem;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
}
.dot { opacity: 0.5; }

/* Responsive */
@media (max-width: 640px) {
  .weather-card { padding: 22px; }
  .card-header { flex-direction: column; align-items: flex-start; }
  .weather-icon { font-size: 3.5rem; }
  .main-temp { gap: 16px; }
  .stats-grid { grid-template-columns: 1fr; }
}
</style>