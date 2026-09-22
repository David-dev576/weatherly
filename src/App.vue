<template>
  <div class="app">
    <Header
      :location-loading="locationLoading"
      :location-active="locationSource === 'gps'"
      @use-location="getUserLocation"
    />

    <main class="container">
      <!-- HERO + SEARCH -->
      <section class="hero slide-up">
        <h1 class="hero-title">What's the weather like today?</h1>
        <p class="hero-sub">
          Discover real-time weather conditions anywhere in the world.
        </p>

        <div class="hero-actions">
          <SearchBar
            v-model="city"
            :loading="loading && locationSource === 'search'"
            @search="searchCity"
          />

          <LocationButton
            :loading="locationLoading"
            :active="locationSource === 'gps'"
            @click="getUserLocation"
          />
        </div>

        <p v-if="validationMessage" class="validation-msg" role="alert">
          {{ validationMessage }}
        </p>
      </section>

      <!-- GPS STATUS (v-show) -->
      <div
        v-show="locationSource === 'gps' && !error && weather"
        class="gps-status fade-in"
        aria-live="polite"
      >
        <span class="gps-dot"></span>
        <i class="fa-solid fa-location-crosshairs" aria-hidden="true"></i>
        <span>Using your current location</span>
      </div>

      <!-- CONTENT ZONE -->
      <section class="content">
        <LoadingMessage v-if="loading" :message="loadingMessage" />

        <ErrorMessage
          v-else-if="error"
          :type="error.type"
          :title="error.title"
          :message="error.message"
          @retry="resetError"
        />

        <WeatherCard
          v-else-if="weather"
          :weather="weather"
          :city="cityName"
          :country="countryName"
          :latitude="latitude"
          :longitude="longitude"
          :location-source="locationSource"
        />

        <EmptyState
          v-else
          @focus-search="focusSearch"
          @use-location="getUserLocation"
        />
      </section>
    </main>

    <footer class="footer">
      <p>
        Built with Vue.js + Open-Meteo API —
        <i class="fa-solid fa-circle footer-dot" aria-hidden="true"></i>
        Real-time data
      </p>
    </footer>
  </div>
</template>

<script>
import Header from './components/Header.vue'
import SearchBar from './components/SearchBar.vue'
import LocationButton from './components/LocationButton.vue'
import WeatherCard from './components/WeatherCard.vue'
import LoadingMessage from './components/LoadingMessage.vue'
import ErrorMessage from './components/ErrorMessage.vue'
import EmptyState from './components/EmptyState.vue'

const GEOCODE_URL = 'https://geocoding-api.open-meteo.com/v1/search'
const WEATHER_URL = 'https://api.open-meteo.com/v1/forecast'

export default {
  name: 'App',
  components: {
    Header, SearchBar, LocationButton, WeatherCard,
    LoadingMessage, ErrorMessage, EmptyState
  },
  data() {
    return {
      city: '',
      validationMessage: '',
      latitude: null,
      longitude: null,
      cityName: '',
      countryName: '',
      locationSource: '',
      weather: null,
      loading: false,
      locationLoading: false,
      error: null
    }
  },
  computed: {
    loadingMessage() {
      return this.locationSource === 'gps'
        ? 'Detecting your location...'
        : 'Fetching weather...'
    }
  },
  methods: {
    async searchCity() {
      this.validationMessage = ''
      this.resetError()
      const query = this.city.trim()

      if (!query) {
        this.validationMessage = 'Please enter a city name.'
        return
      }

      this.loading = true
      this.locationSource = 'search'
      this.weather = null

      try {
        const geoRes = await fetch(
          `${GEOCODE_URL}?name=${encodeURIComponent(query)}&count=1&language=en&format=json`
        )
        if (!geoRes.ok) throw new Error('GEOCODE_HTTP')
        const geoData = await geoRes.json()

        if (!geoData.results || geoData.results.length === 0) {
          this.loading = false
          this.error = {
            type: 'not-found',
            title: 'City not found',
            message: `We couldn't find "${query}". Try searching for another city.`
          }
          return
        }

        const place = geoData.results[0]
        this.latitude = place.latitude
        this.longitude = place.longitude
        this.cityName = place.name
        this.countryName = place.country || ''

        await this.fetchWeather()
      } catch (err) {
        this.loading = false
        this.error = {
          type: 'network',
          title: 'Something went wrong',
          message: 'Please check your internet connection and try again.'
        }
      }
    },

    getUserLocation() {
      this.validationMessage = ''
      this.resetError()

      if (!navigator.geolocation) {
        this.error = {
          type: 'unsupported',
          title: 'Geolocation not supported',
          message: 'Geolocation is not supported by your browser. Please search for a city manually.'
        }
        return
      }

      this.locationLoading = true
      this.locationSource = 'gps'
      this.weather = null

      navigator.geolocation.getCurrentPosition(
        this.handleLocationSuccess,
        this.handleLocationError,
        { enableHighAccuracy: true, timeout: 10000, maximumAge: 0 }
      )
    },

    async handleLocationSuccess(position) {
      this.latitude = position.coords.latitude
      this.longitude = position.coords.longitude
      this.locationLoading = false
      this.loading = true
      this.locationSource = 'gps'

      try {
        const res = await fetch(
          `https://geocoding-api.open-meteo.com/v1/search?latitude=${this.latitude}&longitude=${this.longitude}&count=1&language=en&format=json`
        )
        if (res.ok) {
          const data = await res.json()
          if (data.results && data.results.length > 0) {
            this.cityName = data.results[0].name
            this.countryName = data.results[0].country || ''
          } else {
            this.cityName = 'Your current location'
            this.countryName = ''
          }
        } else {
          this.cityName = 'Your current location'
        }
      } catch (e) {
        this.cityName = 'Your current location'
        this.countryName = ''
      }

      await this.fetchWeather()
    },

    handleLocationError(err) {
      this.locationLoading = false
      this.loading = false

      if (err.code === err.PERMISSION_DENIED) {
        this.error = {
          type: 'gps-denied',
          title: 'Location access denied',
          message: 'Please allow location access or search for a city manually.'
        }
      } else if (err.code === err.POSITION_UNAVAILABLE) {
        this.error = {
          type: 'gps-unavailable',
          title: 'Position unavailable',
          message: 'Unable to determine your location. Please try again or search manually.'
        }
      } else if (err.code === err.TIMEOUT) {
        this.error = {
          type: 'gps-timeout',
          title: 'Location request timed out',
          message: 'The location request took too long. Please try again or search manually.'
        }
      } else {
        this.error = {
          type: 'gps-error',
          title: 'Unable to determine your location',
          message: 'Something went wrong while retrieving your position.'
        }
      }
    },

    async fetchWeather() {
      this.loading = true
      this.resetError()

      const params = new URLSearchParams({
        latitude: this.latitude,
        longitude: this.longitude,
        current: [
          'temperature_2m',
          'relative_humidity_2m',
          'apparent_temperature',
          'is_day',
          'weather_code',
          'wind_speed_10m',
          'surface_pressure'
        ].join(','),
        timezone: 'auto'
      })

      try {
        const res = await fetch(`${WEATHER_URL}?${params.toString()}`)
        if (!res.ok) throw new Error('WEATHER_HTTP')
        const data = await res.json()
        if (!data.current) throw new Error('NO_CURRENT')

        this.weather = {
          temperature: Math.round(data.current.temperature_2m),
          feelsLike: Math.round(data.current.apparent_temperature),
          humidity: data.current.relative_humidity_2m,
          windSpeed: Math.round(data.current.wind_speed_10m),
          pressure: Math.round(data.current.surface_pressure),
          isDay: data.current.is_day === 1,
          weatherCode: data.current.weather_code,
          time: data.current.time
        }
      } catch (err) {
        this.error = {
          type: 'api',
          title: 'Weather service unavailable',
          message: 'We couldn\u2019t fetch the weather data. Please try again in a moment.'
        }
      } finally {
        this.loading = false
      }
    },

    resetError() { this.error = null },

    focusSearch() {
      const input = document.querySelector('#city-input')
      if (input) input.focus()
    }
  }
}
</script>

<style scoped>
.app { min-height: 100vh; display: flex; flex-direction: column; }

.container {
  width: 100%;
  max-width: 1100px;
  margin: 0 auto;
  padding: 0 20px;
  flex: 1;
}

.hero { text-align: center; padding: 48px 0 24px; }
.hero-title {
  font-size: clamp(1.75rem, 4vw, 3rem);
  font-weight: 800;
  letter-spacing: -0.02em;
  color: var(--text);
  line-height: 1.15;
}
.hero-sub {
  margin-top: 12px;
  color: var(--text-2);
  font-size: clamp(0.95rem, 1.5vw, 1.1rem);
  max-width: 560px;
  margin-left: auto;
  margin-right: auto;
}
.hero-actions {
  margin-top: 32px;
  display: flex;
  gap: 12px;
  justify-content: center;
  flex-wrap: wrap;
  align-items: stretch;
}
.validation-msg {
  margin-top: 14px;
  color: var(--error);
  font-size: 0.9rem;
  font-weight: 500;
}

.gps-status {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  margin: 12px auto 0;
  padding: 6px 14px;
  background: rgba(16, 185, 129, 0.1);
  color: #047857;
  border: 1px solid rgba(16, 185, 129, 0.25);
  border-radius: var(--radius-pill);
  font-size: 0.85rem;
  font-weight: 600;
  width: fit-content;
  align-self: center;
}
.gps-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: var(--success);
  animation: pulse 1.6s infinite;
}
.gps-status i { font-size: 0.85rem; }

.content {
  padding: 32px 0 64px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.footer {
  text-align: center;
  padding: 24px;
  color: var(--text-2);
  font-size: 0.85rem;
}
.footer-dot {
  color: var(--success);
  font-size: 0.5rem;
  margin: 0 6px;
  vertical-align: middle;
}

@media (max-width: 640px) {
  .hero { padding: 32px 0 16px; }
  .hero-actions { flex-direction: column; }
  .content { padding: 24px 0 40px; }
}
</style>