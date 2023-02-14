<script>
import axios from 'axios';

export default {
  data: function () {
    return {
      message: "Welcome to Vue.js!",
      lat: 42.8667,
      lon: 88.3334,
      appid: process.env.VUE_APP_API_KEY,
    };
  },
  created: function () {
    this.getWeather();
  },
  methods: {
    getGeoLocation: function () {
      axios.get(`http://api.openweathermap.org/geo/1.0/direct?q={city name},{state code},{country code}&limit={limit}&appid={API key}`).then(response => {
        console.log(response);
      })
    },
    getWeather: function () {
      axios.get(`https://api.openweathermap.org/data/3.0/onecall?lat=33.44&lon=-94.04&exclude=hourly,daily&appid=${this.appid}`).then(response => {
        this.message = response.data.current.weather[0].description;
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