<script setup>
import { ref, onMounted } from 'vue';

const origin = ref('');
const destination = ref('');
const departDate = ref('');
const currency = ref('EUR');
const cheapestFlight = ref(null);
const error = ref(null);
const airports = ref([]);

const getCheapestFlight = async () => {
  try {
    const apiUrl = `https://www.ryanair.com/api/farfnd/v4/oneWayFares/${origin.value}/${destination.value}/cheapestPerDay?outboundMonthOfDate=${departDate.value}&currency=${currency.value}`;
    console.log('API URL:', apiUrl);
    const response = await fetch(apiUrl);

    if (!response.ok) {
      throw new Error('Failed to fetch data');
    }

    const data = await response.json();
    cheapestFlight.value = data.outbound.minFare;
    error.value = null; // Reset error if successful
  } catch (error) {
    console.error('Error fetching cheapest flight:', error);
    error.value = 'Failed to fetch cheapest flight data';
    cheapestFlight.value = null; // Reset cheapestFlight on error
  }
};

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

onMounted(fetchAirports);
</script>

<template>
  <div class="container">
    <h1>Find Cheapest flight {{ departDate }}</h1>
    <form @submit.prevent="getCheapestFlight">
      <div>
        <label for="origin">From</label>
        <select id="origin" v-model="origin">
          <option value="">Departure</option>
          <option v-for="airport in airports" :key="airport.iataCode" :value="airport.iataCode">{{ airport.name }}</option>
        </select>
      </div>
      <div>
        <label for="destination">To</label>
        <select id="destination" v-model="destination">
          <option value="">Destination</option>
          <option v-for="airport in airports" :key="airport.iataCode" :value="airport.iataCode">{{ airport.name }}</option>
        </select>
      </div>
      <div>
        <label for="departDate">Departure date:</label>
        <input type="date" id="departDate" v-model="departDate">
      </div>
      <div>
        <label for="currency">Currency:</label>
        <select id="currency" v-model="currency">
          <option value="EUR">EUR</option>
          <option value="USD">USD</option>
          <option value="PLN">PLN</option>
          <!-- Add more currency options as needed -->
        </select>
      </div>
      <button type="submit">Go!</button>
    </form>

    <div v-if="error" class="error-message">{{ error }}</div>

    <div v-if="cheapestFlight !== null" class="result-container">
      <h2>Cheapest Flight:</h2>
      <p>Departure Date: {{ cheapestFlight.departureDate }}</p>
      <p>Arrival Date: {{ cheapestFlight.arrivalDate }}</p>
      <p>Price: {{ cheapestFlight.price.value }} {{ cheapestFlight.price.currencySymbol }}</p>
    </div>

    <!-- Display message when no flights are found -->
    <div v-else-if="cheapestFlight === null && !error" class="result-container">
      <p>No flights found.</p>
    </div>
  </div>
</template>
