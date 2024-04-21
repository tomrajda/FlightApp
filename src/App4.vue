<template>
  <div class="container">
    <h1>Get Destinations from Airport</h1>
    <form @submit.prevent="getDestinations">
      <div>
        <label for="airportSelect">Select Airport:</label>
        <select id="airportSelect" v-model="selectedAirport">
          <option v-for="airport in airports" :key="airport.iataCode" :value="airport.iataCode">
            {{ airport.name }}
          </option>
        </select>
      </div>
      <button type="submit">Get Destinations</button>
    </form>

    <div v-if="destinations.length > 0" class="destination-list">
      <h2>Destinations from {{ getSelectedAirportName() }}</h2>
      <ul>
        <li v-for="(destination, index) in destinations" :key="index">
          <strong>{{ destination.arrivalAirport.name }}</strong> ({{ destination.arrivalAirport.code }})
          <p>Country: {{ destination.arrivalAirport.country.name }}</p>
        </li>
      </ul>
    </div>

    <div v-if="error" class="error-message">{{ error }}</div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const selectedAirport = ref('');
const airports = ref([]);
const destinations = ref([]);
const error = ref(null);

const fetchAirports = async () => {
  try {
    const apiUrl = 'https://www.ryanair.com/api/views/locate/3/airports/en/active';
    const response = await fetch(apiUrl);

    if (!response.ok) {
      throw new Error('Failed to fetch airports data');
    }

    const data = await response.json();
    airports.value = data;
  } catch (error) {
    console.error('Error fetching airports:', error);
    error.value = 'Failed to fetch airports data';
    airports.value = [];
  }
};

const getDestinations = async () => {
  try {
    const apiUrl = `https://www.ryanair.com/api/views/locate/searchWidget/routes/en/airport/${selectedAirport.value}`;
    console.log('API URL:', apiUrl);

    const response = await fetch(apiUrl);

    if (!response.ok) {
      throw new Error('Failed to fetch data');
    }

    const data = await response.json();
    destinations.value = data;
    error.value = null; // Reset error if successful
  } catch (error) {
    console.error('Error fetching destinations:', error);
    error.value = 'Failed to fetch destination data';
    destinations.value = []; // Reset destinations on error
  }
};

const getSelectedAirportName = () => {
  const selected = airports.value.find(airport => airport.iataCode === selectedAirport.value);
  return selected ? selected.name : '';
};

onMounted(fetchAirports);
</script>