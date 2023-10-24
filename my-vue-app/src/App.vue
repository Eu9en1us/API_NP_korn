<template>
  <div class="container">
    <h1>Пошук поштових відділень</h1>
    <div class="input-container">
      <label for="city" class="label">Місто:</label>
      <select id="city" class="select"></select>
    </div>
    <div class="input-container">
      <label for="branch" class="label">Поштове відділення:</label>
      <select id="branch" class="select"></select>
    </div>
  </div>
</template>
<script>
import { ref, onMounted } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const apiUrl = 'https://api.novaposhta.ua/v2.0/json';
    const apiKey = '15b19b63317baf7fb2d45390b670d8cc';

    const cities = ref([]);
    const selectedCity = ref('');
    const branches = ref([]);
    const selectedBranch = ref('');

    const fetchCities = async () => {
      try {
        const requestData = {
          apiKey: apiKey,
          modelName: 'Address',
          calledMethod: 'getCities',
          methodProperties: {}
        };

        const response = await axios.post(apiUrl, requestData);
        cities.value = response.data.data;
      } catch (error) {
        console.error('Помилка при запиті до API Нової Пошти:', error);
      }
    };

    const fetchBranches = async () => {
      if (selectedCity.value) {
        try {
          const requestData = {
            apiKey: apiKey,
            modelName: 'AddressGeneral',
            calledMethod: 'getWarehouses',
            methodProperties: {
              CityRef: selectedCity.value
            }
          };

          const response = await axios.post(apiUrl, requestData);
          branches.value = response.data.data;
        } catch (error) {
          console.error('Помилка при запиті до API Нової Пошти:', error);
        }
      }
    };

    onMounted(fetchCities);

    return {
      cities,
      selectedCity,
      branches,
      selectedBranch,
      fetchBranches
    };
  }
};
</script>