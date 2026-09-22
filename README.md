# ☁️ Weatherly — Real-time Weather App

A modern, responsive weather application built with **Vue.js (Options API)** and **JavaScript**, powered by the **Open-Meteo API** and the **Browser Geolocation API**.

![Weatherly](https://img.shields.io/badge/Vue.js-3.x-42b883?style=for-the-badge&logo=vue.js)
![JavaScript](https://img.shields.io/badge/JavaScript-ES2022-f7df1e?style=for-the-badge&logo=javascript)
![Open-Meteo](https://img.shields.io/badge/API-Open--Meteo-2563EB?style=for-the-badge)

---

## ✨ Features

- 🔎 **City search** — powered by Open-Meteo Geocoding API
- 📍 **GPS / My Location** — using the native `navigator.geolocation` API
- 🌡️ **Real-time weather** — temperature, humidity, wind, feels-like, pressure
- 🎨 **Premium UI** — modern dashboard, animations, responsive design
- ⚡ **Robust error handling** — city not found, network errors, GPS denied, unsupported browsers
- ♿ **Accessible** — ARIA labels, keyboard navigation, focus states
- 📱 **Fully responsive** — 320px → 1440px

---

## 🛠️ Technologies

- **Vue.js 3** — Options API only (no Composition API)
- **JavaScript (ES2022)** — no TypeScript
- **Open-Meteo API** — Geocoding + Forecast (free, no API key)
- **Browser Geolocation API**
- **Vite** — build tool

---

## 🚀 Installation

```bash
# Clone the repository
git clone <YOUR_REPO_URL>
cd weatherly

# Install dependencies
npm install

# Run in development
npm run dev

# Build for production
npm run build
```

---

## 🔄 How It Works

### 🔎 City Search flow

```
City name
  → Open-Meteo Geocoding API
  → latitude + longitude + country
  → Open-Meteo Forecast API
  → Weather card
```

### 📍 GPS flow

```
"Use my location" click
  → navigator.geolocation.getCurrentPosition()
  → latitude + longitude
  → (Optional) reverse geocoding for city name
  → Open-Meteo Forecast API
  → Weather card
```

---

## 📁 Project Structure

```
src/
├── assets/
│   └── main.css
├── components/
│   ├── Header.vue
│   ├── SearchBar.vue
│   ├── LocationButton.vue
│   ├── WeatherCard.vue
│   ├── WeatherStat.vue
│   ├── LoadingMessage.vue
│   ├── ErrorMessage.vue
│   └── EmptyState.vue
├── App.vue
└── main.js
```

---

## 🌐 Deployment (Vercel)

1. Push the repository to GitHub.
2. Go to [vercel.com](https://vercel.com) → **New Project**.
3. Import your GitHub repository.
4. Framework preset: **Vite**.
5. Build command: `npm run build` — Output: `dist`.
6. Click **Deploy**.
7. Test the production URL.

---

## 🔒 Privacy

This app **does not store**, **does not transmit**, and **does not track** your GPS coordinates. They are used only, in-memory, to fetch weather data from Open-Meteo.

---

## 📜 License

MIT — Free to use for educational and commercial purposes.