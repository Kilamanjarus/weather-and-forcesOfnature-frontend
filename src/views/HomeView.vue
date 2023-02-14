<script>
import axios from 'axios';

export default {
  data: function () {
    return {
      message: "Welcome to Vue.js!",
      lat: 42.8667,
      lon: 88.3334,
      address: { city_name: 'New York', state_code: 'NY', country_code: 'US' },
    };
  },
  created: function () {
    this.getWeather();
    this.getGeocode();
  },
  methods: {
    getGeocode: function () {
      axios.get(`http://api.openweathermap.org/geo/1.0/direct?q=${this.address.city_name},${this.address.state_code},${this.address.country_code}&limit=3&appid=${process.env.VUE_APP_WEATHER_API}`).then(response => {
        console.log(response.data);
      })
    },
    getWeather: function () {
      console.log(process.env.VUE_APP_WEATHER_API);
      axios.get(`https://api.openweathermap.org/data/2.5/weather?q=peshawar&appid=${process.env.VUE_APP_WEATHER_API}`).then(response => {
        this.message = response.data;
        console.log(response.data);
      })
    }
  },
};
</script>

<template>
  <div class="home">
    <h1>{{ message }}</h1>
  </div>
</template>

<style>

</style>