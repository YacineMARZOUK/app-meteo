<script setup>
import { ref, watch } from 'vue';
import axios from 'axios';

const emit = defineEmits(['search', 'geolocation']);
const searchQuery = ref('');
const geoLoading = ref(false);
const geoError = ref(null);
const suggestions = ref([]);
const isLoading = ref(false);

const handleSubmit = (e) => {
  e.preventDefault();
  if (searchQuery.value.trim()) {
    emit('search', searchQuery.value.trim());
    suggestions.value = [];
  }
};

const fetchSuggestions = async (query) => {
  if (query.length < 2) {
    suggestions.value = [];
    return;
  }
  
  isLoading.value = true;
  try {
    // Using OpenWeatherMap Geo API to get city suggestions
    const response = await axios.get(
      `https://api.openweathermap.org/geo/1.0/direct?q=${query}&limit=5&appid=1635890035cbba097fd5c26c8ea672a1`
    );
    
    suggestions.value = response.data.map(city => ({
      name: city.name,
      country: city.country,
      state: city.state,
      lat: city.lat,
      lon: city.lon
    }));
  } catch (error) {
    console.error('Error fetching city suggestions:', error);
    suggestions.value = [];
  } finally {
    isLoading.value = false;
  }
};

const selectSuggestion = (suggestion) => {
  searchQuery.value = suggestion.name;
  emit('geolocation', { lat: suggestion.lat, lon: suggestion.lon });
  suggestions.value = [];
};

// Debounce function to limit API calls while typing
let debounceTimeout;
watch(searchQuery, (newValue) => {
  clearTimeout(debounceTimeout);
  debounceTimeout = setTimeout(() => {
    if (newValue.trim()) {
      fetchSuggestions(newValue.trim());
    } else {
      suggestions.value = [];
    }
  }, 300);
});

const handleGeolocation = () => {
  if (!navigator.geolocation) {
    geoError.value = "La géolocalisation n'est pas prise en charge par votre navigateur";
    return;
  }

  geoLoading.value = true;
  geoError.value = null;

  navigator.geolocation.getCurrentPosition(
    (position) => {
      geoLoading.value = false;
      const { latitude, longitude } = position.coords;
      emit('geolocation', { lat: latitude, lon: longitude });
    },
    (error) => {
      geoLoading.value = false;
      
      switch (error.code) {
        case error.PERMISSION_DENIED:
          geoError.value = "Vous avez refusé la demande de géolocalisation";
          break;
        case error.POSITION_UNAVAILABLE:
          geoError.value = "Les informations de localisation ne sont pas disponibles";
          break;
        case error.TIMEOUT:
          geoError.value = "La demande de localisation a expiré";
          break;
        default:
          geoError.value = "Une erreur inconnue s'est produite";
          break;
      }
    }
  );
};
</script>

<template>
  <div class="search-wrapper">
    <div class="search-container-wrapper">
      <form @submit="handleSubmit" class="search-form">
        <div class="search-container">
          <input
            type="text"
            v-model="searchQuery"
            placeholder="Rechercher une ville..."
            class="search-input"
            autocomplete="off"
          />
          <button type="submit" class="search-button">
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="22" height="22" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
              <circle cx="11" cy="11" r="8"></circle>
              <line x1="21" y1="21" x2="16.65" y2="16.65"></line>
            </svg>
          </button>
        </div>
      </form>
      
      <div class="suggestions-container" v-if="suggestions.length > 0">
        <ul class="suggestions-list">
          <li 
            v-for="(suggestion, index) in suggestions" 
            :key="index" 
            @click="selectSuggestion(suggestion)"
            class="suggestion-item"
          >
            <span class="suggestion-name">{{ suggestion.name }}</span>
            <span class="suggestion-details">
              {{ suggestion.state ? suggestion.state + ', ' : '' }}{{ suggestion.country }}
            </span>
          </li>
        </ul>
      </div>
      
      <div v-if="isLoading" class="suggestion-loading">Recherche de villes...</div>
    </div>
    
    <div class="geo-container">
      <button @click="handleGeolocation" class="geo-button" :disabled="geoLoading">
        <svg v-if="!geoLoading" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <circle cx="12" cy="12" r="10"></circle>
          <polygon points="16.24 7.76 14.12 14.12 7.76 16.24 9.88 9.88 16.24 7.76"></polygon>
        </svg>
        <span v-if="geoLoading">Localisation...</span>
        <span v-else>Ma position</span>
      </button>
      
      <div v-if="geoError" class="geo-error">{{ geoError }}</div>
    </div>
  </div>
</template>

<style scoped>
.search-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
  width: 100%;
  padding: 24px 16px;
  background: linear-gradient(to bottom, #1e3c72, #2a5298);
  border-radius: 16px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
}

.search-container-wrapper {
  position: relative;
  width: 100%;
  max-width: 550px;
}

.search-form {
  width: 100%;
}

.search-container {
  display: flex;
  position: relative;
}

.search-input {
  flex: 1;
  padding: 16px 24px;
  font-size: 1.1rem;
  border: none;
  border-radius: 16px;
  background-color: rgba(255, 255, 255, 0.9);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  transition: all 0.4s ease;
  color: #1e293b;
  font-weight: 400;
  border: 2px solid transparent;
}

.search-input::placeholder {
  color: rgba(30, 41, 59, 0.6);
  font-weight: 300;
}

.search-input:focus {
  outline: none;
  box-shadow: 0 8px 16px rgba(255, 255, 255, 0.25);
  border-color: rgba(255, 255, 255, 0.5);
  transform: translateY(-2px);
  background-color: white;
}

.search-button {
  position: absolute;
  right: 8px;
  top: 50%;
  transform: translateY(-50%);
  background: linear-gradient(135deg, #4a6bbd, #2a5298);
  border: none;
  color: white;
  width: 46px;
  height: 46px;
  border-radius: 14px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
}

.search-button:hover {
  background: linear-gradient(135deg, #5a7bcf, #3a62a8);
  transform: translateY(-50%) scale(1.05);
  box-shadow: 0 6px 15px rgba(0, 0, 0, 0.3);
}

.search-button:active {
  transform: translateY(-50%) scale(0.98);
}

.suggestions-container {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  margin-top: 8px;
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  border-radius: 14px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
  overflow: hidden;
  z-index: 10;
  animation: fadeIn 0.2s ease-out;
  border: 1px solid rgba(255, 255, 255, 0.3);
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(-10px); }
  to { opacity: 1; transform: translateY(0); }
}

.suggestions-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.suggestion-item {
  padding: 14px 20px;
  cursor: pointer;
  display: flex;
  justify-content: space-between;
  align-items: center;
  transition: all 0.2s ease;
}

.suggestion-item:hover {
  background-color: rgba(74, 107, 189, 0.1);
}

.suggestion-item:active {
  background-color: rgba(74, 107, 189, 0.2);
}

.suggestion-item:not(:last-child) {
  border-bottom: 1px solid rgba(74, 107, 189, 0.15);
}

.suggestion-name {
  font-weight: 500;
  color: #1e293b;
}

.suggestion-details {
  color: #64748b;
  font-size: 0.9rem;
}

.suggestion-loading {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  margin-top: 8px;
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  border-radius: 14px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
  padding: 14px;
  text-align: center;
  color: #4a6bbd;
  font-size: 0.95rem;
  animation: pulse 1.5s infinite ease-in-out;
  border: 1px solid rgba(255, 255, 255, 0.3);
}

@keyframes pulse {
  0% { opacity: 0.6; }
  50% { opacity: 1; }
  100% { opacity: 0.6; }
}

.geo-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
}

.geo-button {
  background-color: rgba(255, 255, 255, 0.25);
  border: 1px solid rgba(255, 255, 255, 0.3);
  border-radius: 12px;
  padding: 12px 20px;
  font-size: 1rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  gap: 8px;
  color: white;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  backdrop-filter: blur(10px);
}

.geo-button:hover:not(:disabled) {
  background-color: rgba(255, 255, 255, 0.35);
  transform: translateY(-3px);
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
}

.geo-button:active:not(:disabled) {
  transform: translateY(-1px);
  background-color: rgba(255, 255, 255, 0.2);
}

.geo-button:disabled {
  opacity: 0.65;
  cursor: not-allowed;
  background-color: rgba(148, 163, 184, 0.5);
}

.geo-error {
  color: white;
  font-size: 0.9rem;
  text-align: center;
  max-width: 320px;
  background-color: rgba(220, 38, 38, 0.9);
  padding: 10px 16px;
  border-radius: 8px;
  animation: shake 0.5s ease-in-out;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.25);
}

@keyframes shake {
  0% { transform: translateX(0); }
  20% { transform: translateX(-5px); }
  40% { transform: translateX(5px); }
  60% { transform: translateX(-3px); }
  80% { transform: translateX(3px); }
  100% { transform: translateX(0); }
}
</style>



<style scoped>
.search-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
  width: 100%;
  padding: 24px 16px;
  background: linear-gradient(to bottom, #1e3c72, #2a5298);
  border-radius: 16px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
}

.search-container-wrapper {
  position: relative;
  width: 100%;
  max-width: 550px;
}

.search-form {
  width: 100%;
}

.search-container {
  display: flex;
  position: relative;
}

.search-input {
  flex: 1;
  padding: 16px 24px;
  font-size: 1.1rem;
  border: none;
  border-radius: 16px;
  background-color: rgba(255, 255, 255, 0.9);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  transition: all 0.4s ease;
  color: #1e293b;
  font-weight: 400;
  border: 2px solid transparent;
}

.search-input::placeholder {
  color: rgba(30, 41, 59, 0.6);
  font-weight: 300;
}

.search-input:focus {
  outline: none;
  box-shadow: 0 8px 16px rgba(255, 255, 255, 0.25);
  border-color: rgba(255, 255, 255, 0.5);
  transform: translateY(-2px);
  background-color: white;
}

.search-button {
  position: absolute;
  right: 8px;
  top: 50%;
  transform: translateY(-50%);
  background: linear-gradient(135deg, #4a6bbd, #2a5298);
  border: none;
  color: white;
  width: 46px;
  height: 46px;
  border-radius: 14px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
}

.search-button:hover {
  background: linear-gradient(135deg, #5a7bcf, #3a62a8);
  transform: translateY(-50%) scale(1.05);
  box-shadow: 0 6px 15px rgba(0, 0, 0, 0.3);
}

.search-button:active {
  transform: translateY(-50%) scale(0.98);
}

.suggestions-container {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  margin-top: 8px;
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  border-radius: 14px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
  overflow: hidden;
  z-index: 10;
  animation: fadeIn 0.2s ease-out;
  border: 1px solid rgba(255, 255, 255, 0.3);
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(-10px); }
  to { opacity: 1; transform: translateY(0); }
}

.suggestions-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.suggestion-item {
  padding: 14px 20px;
  cursor: pointer;
  display: flex;
  justify-content: space-between;
  align-items: center;
  transition: all 0.2s ease;
}

.suggestion-item:hover {
  background-color: rgba(74, 107, 189, 0.1);
}

.suggestion-item:active {
  background-color: rgba(74, 107, 189, 0.2);
}

.suggestion-item:not(:last-child) {
  border-bottom: 1px solid rgba(74, 107, 189, 0.15);
}

.suggestion-name {
  font-weight: 500;
  color: #1e293b;
}

.suggestion-details {
  color: #64748b;
  font-size: 0.9rem;
}

.suggestion-loading {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  margin-top: 8px;
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  border-radius: 14px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
  padding: 14px;
  text-align: center;
  color: #4a6bbd;
  font-size: 0.95rem;
  animation: pulse 1.5s infinite ease-in-out;
  border: 1px solid rgba(255, 255, 255, 0.3);
}

@keyframes pulse {
  0% { opacity: 0.6; }
  50% { opacity: 1; }
  100% { opacity: 0.6; }
}

.geo-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
}

.geo-button {
  background-color: rgba(255, 255, 255, 0.25);
  border: 1px solid rgba(255, 255, 255, 0.3);
  border-radius: 12px;
  padding: 12px 20px;
  font-size: 1rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  gap: 8px;
  color: white;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  backdrop-filter: blur(10px);
}

.geo-button:hover:not(:disabled) {
  background-color: rgba(255, 255, 255, 0.35);
  transform: translateY(-3px);
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
}

.geo-button:active:not(:disabled) {
  transform: translateY(-1px);
  background-color: rgba(255, 255, 255, 0.2);
}

.geo-button:disabled {
  opacity: 0.65;
  cursor: not-allowed;
  background-color: rgba(148, 163, 184, 0.5);
}

.geo-error {
  color: white;
  font-size: 0.9rem;
  text-align: center;
  max-width: 320px;
  background-color: rgba(220, 38, 38, 0.9);
  padding: 10px 16px;
  border-radius: 8px;
  animation: shake 0.5s ease-in-out;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.25);
}

@keyframes shake {
  0% { transform: translateX(0); }
  20% { transform: translateX(-5px); }
  40% { transform: translateX(5px); }
  60% { transform: translateX(-3px); }
  80% { transform: translateX(3px); }
  100% { transform: translateX(0); }
}
</style>