<script setup>
import { ref, onMounted } from 'vue';

const endpointUrl = 'https://www.ryanair.com/api/views/locate/3/countries/en';
const countries = ref([]); // Reactive variable to hold the country data

onMounted(async () => {
  try {
    const response = await fetch(endpointUrl);
    if (!response.ok) {
      throw new Error('Failed to fetch data');
    }
    const data = await response.json();
    if (Array.isArray(data)) {
      countries.value = data; // Update countries with API response
    } else {
      console.log('No country data found');
    }
  } catch (error) {
    console.error('Error fetching data:', error);
  }
});
</script>

<template>
  <div>
    <h1>Country List</h1>
    <ul>
      <li v-for="country in countries" :key="country.code">
        {{ country.name }} ({{ country.currency }}) is in Schengen: {{ country.schengen }}
      </li>
    </ul>
  </div>
</template>


