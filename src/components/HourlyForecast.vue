<script setup>
import { computed } from 'vue';

const props = defineProps({
  forecastData: {
    type: Object,
    required: true
  }
});

const hourlyForecast = computed(() => {
  // Get the next 12 hours (8 records with 3-hour intervals)
  return props.forecastData.list.slice(0, 8).map(item => {
    const date = new Date(item.dt * 1000);
    return {
      time: date.toLocaleTimeString('fr-FR', { hour: '2-digit', minute: '2-digit' }),
      day: date.toLocaleDateString('fr-FR', { weekday: 'short' }),
      temp: Math.round(item.main.temp),
      icon: item.weather[0].icon,
      description: item.weather[0].description
    };
  });
});
</script>

<template>
  <div class="hourly-forecast">
    <h3>Prévisions à court terme</h3>
    
    <div class="forecast-container">
      <div 
        v-for="(forecast, index) in hourlyForecast" 
        :key="index" 
        class="forecast-item"
      >
        <div class="forecast-time">{{ forecast.day }} {{ forecast.time }}</div>
        <img 
          :src="`https://openweathermap.org/img/wn/${forecast.icon}@2x.png`" 
          :alt="forecast.description" 
          class="forecast-icon"
        />
        <div class="forecast-temp">{{ forecast.temp }}°C</div>
        <div class="forecast-desc">{{ forecast.description }}</div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.hourly-forecast {
  margin-top: 20px;
  padding: 24px;
  border-radius: 16px;
  background: linear-gradient(to bottom, #1e3c72, #2a5298);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.15);
  color: #fff;
}

h3 {
  margin-top: 0;
  margin-bottom: 20px;
  font-size: 1.7rem;
  font-weight: 600;
  text-align: center;
  color: #fff;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.forecast-container {
  display: flex;
  overflow-x: auto;
  gap: 18px;
  padding: 15px 5px;
  scrollbar-width: thin;
  scrollbar-color: rgba(255, 255, 255, 0.3) transparent;
}

.forecast-container::-webkit-scrollbar {
  height: 6px;
}

.forecast-container::-webkit-scrollbar-track {
  background: rgba(255, 255, 255, 0.1);
  border-radius: 10px;
}

.forecast-container::-webkit-scrollbar-thumb {
  background-color: rgba(255, 255, 255, 0.3);
  border-radius: 10px;
}

.forecast-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  min-width: 110px;
  padding: 15px 12px;
  border-radius: 12px;
  background-color: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(10px);
  transition: all 0.3s ease;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.forecast-item:hover {
  transform: translateY(-5px);
  background-color: rgba(255, 255, 255, 0.25);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

.forecast-time {
  font-size: 0.9rem;
  font-weight: 500;
  color: rgba(255, 255, 255, 0.9);
  text-align: center;
  margin-bottom: 5px;
}

.forecast-icon {
  margin: 5px 0;
  width: 64px;
  height: 64px;
  filter: drop-shadow(0 2px 4px rgba(0, 0, 0, 0.2));
}

.forecast-temp {
  font-size: 1.4rem;
  font-weight: 700;
  color: rgba(255, 255, 255, 1);
  margin-bottom: 4px;
}

.forecast-desc {
  font-size: 0.8rem;
  color: rgba(255, 255, 255, 0.85);
  text-align: center;
  max-width: 100%;
  overflow: hidden;
  text-overflow: ellipsis;
}

@media (max-width: 600px) {
  .hourly-forecast {
    padding: 16px;
  }
  
  .forecast-item {
    min-width: 90px;
    padding: 10px 8px;
  }
  
  .forecast-time {
    font-size: 0.8rem;
  }
  
  .forecast-icon {
    width: 50px;
    height: 50px;
  }
  
  .forecast-temp {
    font-size: 1.2rem;
  }
  
  .forecast-desc {
    font-size: 0.7rem;
  }
}
</style>