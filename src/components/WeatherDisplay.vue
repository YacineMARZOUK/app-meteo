<script setup>
import { computed } from 'vue';

const props = defineProps({
  weatherData: {
    type: Object,
    required: true
  }
});

const temperature = computed(() => Math.round(props.weatherData.main.temp));
const feelsLike = computed(() => Math.round(props.weatherData.main.feels_like));
const weatherDescription = computed(() => 
  props.weatherData.weather[0].description.charAt(0).toUpperCase() + 
  props.weatherData.weather[0].description.slice(1)
);
const humidity = computed(() => props.weatherData.main.humidity);
const windSpeed = computed(() => Math.round(props.weatherData.wind.speed * 3.6)); // Convert m/s to km/h
const iconCode = computed(() => props.weatherData.weather[0].icon);
const iconUrl = computed(() => `https://openweathermap.org/img/wn/${iconCode.value}@2x.png`);
const date = computed(() => {
  const now = new Date();
  return now.toLocaleDateString('fr-FR', { 
    weekday: 'long', 
    year: 'numeric', 
    month: 'long', 
    day: 'numeric' 
  });
});
const time = computed(() => {
  const now = new Date();
  return now.toLocaleTimeString('fr-FR', { 
    hour: '2-digit', 
    minute: '2-digit' 
  });
});
</script>

<template>
  <div class="weather-display">
    <div class="weather-header">
      <h2>{{ weatherData.name }}, {{ weatherData.sys.country }}</h2>
      <p class="date-time">{{ date }}, {{ time }}</p>
    </div>
    
    <div class="weather-main">
      <div class="temp-container">
        <div class="temperature">{{ temperature }}°C</div>
        <div class="feels-like">Ressenti: {{ feelsLike }}°C</div>
      </div>
      
      <div class="weather-icon">
        <img :src="iconUrl" :alt="weatherDescription" />
        <div class="description">{{ weatherDescription }}</div>
      </div>
    </div>
    
    <div class="weather-details">
      <div class="detail">
        <div class="detail-icon">
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M12 2v6M12 22v-6M4.93 4.93l4.24 4.24M14.83 14.83l4.24 4.24M2 12h6M22 12h-6M4.93 19.07l4.24-4.24M14.83 9.17l4.24-4.24"></path>
          </svg>
        </div>
        <div class="detail-info">
          <div class="detail-label">Humidité</div>
          <div class="detail-value">{{ humidity }}%</div>
        </div>
      </div>
      
      <div class="detail">
        <div class="detail-icon">
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M9.59 4.59A2 2 0 1 1 11 8H2m10.59 11.41A2 2 0 1 0 14 16H2m15.73-8.27A2.5 2.5 0 1 1 19.5 12H2"></path>
          </svg>
        </div>
        <div class="detail-info">
          <div class="detail-label">Vent</div>
          <div class="detail-value">{{ windSpeed }} km/h</div>
        </div>
      </div>
      
      <div class="detail">
        <div class="detail-icon">
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M12 2a10 10 0 1 0 0 20 10 10 0 0 0 0-20z"></path>
            <path d="M12 12v6"></path>
            <path d="M12 6v2"></path>
          </svg>
        </div>
        <div class="detail-info">
          <div class="detail-label">Pression</div>
          <div class="detail-value">{{ weatherData.main.pressure }} hPa</div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.weather-display {
  display: flex;
  flex-direction: column;
  gap: 24px;
  padding: 28px;
  border-radius: 16px;
  background: linear-gradient(to bottom, #1e3c72, #2a5298);
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
  color: #fff;
}

.weather-header {
  text-align: center;
  margin-bottom: 5px;
}

.weather-header h2 {
  margin: 0;
  font-size: 2rem;
  color: #fff;
  font-weight: 600;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.date-time {
  color: rgba(255, 255, 255, 0.85);
  margin: 8px 0 0;
  font-size: 1.1rem;
}

.weather-main {
  display: flex;
  justify-content: space-around;
  align-items: center;
  padding: 15px 0;
  background-color: rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.temp-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.temperature {
  font-size: 4rem;
  font-weight: bold;
  color: #fff;
  text-shadow: 0 2px 6px rgba(0, 0, 0, 0.2);
  line-height: 1;
}

.feels-like {
  font-size: 1.1rem;
  color: rgba(255, 255, 255, 0.85);
  margin-top: 5px;
}

.weather-icon {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.weather-icon img {
  width: 120px;
  height: 120px;
  filter: drop-shadow(0 2px 6px rgba(0, 0, 0, 0.2));
}

.description {
  text-align: center;
  font-size: 1.4rem;
  margin-top: 5px;
  color: #fff;
  font-weight: 500;
}

.weather-details {
  display: flex;
  justify-content: space-between;
  gap: 15px;
  padding: 20px;
  border-radius: 12px;
  background-color: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.detail {
  display: flex;
  align-items: center;
  gap: 12px;
  flex: 1;
  padding: 10px;
  border-radius: 10px;
  transition: all 0.3s ease;
}

.detail:hover {
  background-color: rgba(255, 255, 255, 0.1);
}

.detail-icon {
  font-size: 1.5rem;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background-color: rgba(255, 255, 255, 0.2);
  color: #fff;
}

.detail-icon svg {
  width: 22px;
  height: 22px;
  stroke: currentColor;
}

.detail-info {
  display: flex;
  flex-direction: column;
}

.detail-label {
  font-size: 0.9rem;
  color: rgba(255, 255, 255, 0.75);
}

.detail-value {
  font-size: 1.25rem;
  font-weight: 600;
  color: #fff;
}

@media (max-width: 600px) {
  .weather-display {
    padding: 20px;
    gap: 18px;
  }
  
  .weather-main {
    flex-direction: column;
    gap: 20px;
    padding: 20px 10px;
  }
  
  .weather-details {
    flex-direction: column;
    gap: 10px;
    padding: 15px;
  }
  
  .weather-header h2 {
    font-size: 1.6rem;
  }
  
  .temperature {
    font-size: 3.2rem;
  }
  
  .weather-icon img {
    width: 100px;
    height: 100px;
  }
  
  .description {
    font-size: 1.2rem;
  }
}
</style>