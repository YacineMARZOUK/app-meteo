<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import WeatherDisplay from './components/WeatherDisplay.vue';
import CitySearch from './components/CitySearch.vue';
import HourlyForecast from './components/HourlyForecast.vue';
import DailyForecast from './components/DailyForecast.vue';

const apiKey = '1635890035cbba097fd5c26c8ea672a1'; // OpenWeatherMap API key
const weatherData = ref(null);
const forecastData = ref(null);
const loading = ref(false);
const error = ref(null);
const city = ref('Paris'); // Default city

const fetchWeather = async (cityName) => {
  loading.value = true;
  error.value = null;
  try {
    // Current weather
    const weatherResponse = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?q=${cityName}&appid=${apiKey}&units=metric`
    );
    weatherData.value = weatherResponse.data;
    
    // Forecast data
    const forecastResponse = await axios.get(
      `https://api.openweathermap.org/data/2.5/forecast?q=${cityName}&appid=${apiKey}&units=metric`
    );
    forecastData.value = forecastResponse.data;
  } catch (err) {
    error.value = 'Impossible de trouver cette ville. Veuillez vérifier le nom et réessayer.';
    console.error('Error fetching weather data:', err);
  } finally {
    loading.value = false;
  }
};

const fetchWeatherByCoords = async (coords) => {
  loading.value = true;
  error.value = null;
  try {
    // Current weather by coordinates
    const weatherResponse = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?lat=${coords.lat}&lon=${coords.lon}&appid=${apiKey}&units=metric`
    );
    weatherData.value = weatherResponse.data;
    
    // Set the city name from the API response
    city.value = weatherResponse.data.name;
    
    // Forecast data by coordinates
    const forecastResponse = await axios.get(
      `https://api.openweathermap.org/data/2.5/forecast?lat=${coords.lat}&lon=${coords.lon}&appid=${apiKey}&units=metric`
    );
    forecastData.value = forecastResponse.data;
  } catch (err) {
    error.value = 'Erreur lors de la récupération des données météo pour votre position.';
    console.error('Error fetching weather data by coordinates:', err);
  } finally {
    loading.value = false;
  }
};

const handleCitySearch = (newCity) => {
  city.value = newCity;
  fetchWeather(newCity);
};

const handleGeolocation = (coords) => {
  fetchWeatherByCoords(coords);
};

onMounted(() => {
  fetchWeather(city.value);
});
</script>

<template>
  <div class="app-container">
    <header>
      <h1>Météo App</h1>
      <CitySearch @search="handleCitySearch" @geolocation="handleGeolocation" />
    </header>
    
    <main>
      <div v-if="loading" class="loading">Chargement...</div>
      <div v-else-if="error" class="error">{{ error }}</div>
      <div v-else-if="weatherData" class="weather-content">
        <WeatherDisplay :weather-data="weatherData" />
        <HourlyForecast v-if="forecastData" :forecast-data="forecastData" />
        <DailyForecast v-if="forecastData" :forecast-data="forecastData" />
      </div>
    </main>
    
    <footer>
      <p>Données météorologiques fournies par OpenWeatherMap</p>
    </footer>
  </div>
</template>

<style>
body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  margin: 0;
  padding: 0;
  background: linear-gradient(135deg, #6e8efb, #a777e3);
  min-height: 100vh;
  color: #333;
}

.app-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

header {
  text-align: center;
  margin-bottom: 30px;
}

header h1 {
  color: white;
  margin-bottom: 20px;
  font-size: 2.5rem;
  text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.2);
}

main {
  background-color: rgba(255, 255, 255, 0.9);
  border-radius: 12px;
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
  padding: 20px;
  margin-bottom: 30px;
}

.weather-content {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.loading {
  text-align: center;
  padding: 40px;
  font-size: 1.2rem;
  color: #555;
}

.error {
  text-align: center;
  padding: 20px;
  color: #e74c3c;
  background-color: #fde2e2;
  border-radius: 8px;
  margin: 20px 0;
}

footer {
  text-align: center;
  padding: 20px 0;
  color: rgba(255, 255, 255, 0.8);
  font-size: 0.9rem;
}

@media (max-width: 768px) {
  .weather-content {
    flex-direction: column;
  }
}
</style>
