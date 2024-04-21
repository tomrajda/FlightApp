<script setup>
import { ref } from 'vue';

// Reactive variables for input fields
const origin = ref('');
const destination = ref('');
const availableDates = ref([]);

// Function to fetch available dates based on origin and destination
const findOneWayFlyDate = async () => {
  try {
    const apiUrl = `https://www.ryanair.com/api/farfnd/v4/oneWayFares/${origin.value}/${destination.value}/availabilities`;
    const response = await fetch(apiUrl);

    if (!response.ok) {
      throw new Error('Failed to fetch data');
    }

    const data = await response.json();
    availableDates.value = data; // Update availableDates with API response
  } catch (error) {
    console.error('Error fetching data:', error);
  }
};
</script>

<template>
  <div>
    <h1>Find Available Dates</h1>
    <div>
      <label for="origin">Origin:</label>
      <input type="text" id="origin" v-model="origin">
    </div>
    <div>
      <label for="destination">Destination:</label>
      <input type="text" id="destination" v-model="destination">
    </div>
    <button @click="findOneWayFlyDate">Search</button>

    <div v-if="availableDates.length > 0">
      <h2>Available Dates:</h2>
      <ul>
        <li v-for="date in availableDates" :key="date">{{ date }}</li>
      </ul>
    </div>
  </div>
</template>