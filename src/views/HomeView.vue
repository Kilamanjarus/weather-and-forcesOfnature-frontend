<script>
import axios from 'axios';

export default {
  data: function () {
    return {
      message: "Welcome to Vue.js!",
      lat: 43,
      lon: -75,
      address: { city_name: 'New York', state_code: 'NY', country_code: 'US' },
      map: null,
      imageUrl: '',
      daily: [],
      hourly: [],
      minutely: [],
      current: [],
      icon: "",
      temperatures: {}
    };
  },
  created: function () {
    this.getWeather();
    this.getGeocode();
  },
  methods: {
    getGeocode: function () {
      axios.get(`http://api.openweathermap.org/geo/1.0/direct?q=${this.address.city_name},${this.address.state_code},${this.address.country_code}&limit=3&appid=${process.env.VUE_APP_WEATHER_API}`).then(response => {
        // console.log(response.data);
      })
    },
    getWeather: async function () {
      axios.get(`https://api.openweathermap.org/data/3.0/onecall?lat=${this.lat}&lon=${this.lon}&appid=${process.env.VUE_APP_WEATHER_API}`).then(response => {
        // console.log(response.data);
        this.daily = response.data.daily;
        this.hourly = response.data.hourly;
        this.minutely = response.data.minutely;
        this.message = response.data.alerts;
        this.current = response.data.current;
        this.icon = `http://openweathermap.org/img/wn/${response.data.current.weather[0].icon}@2x.png`;
        this.temperatures.kelvin = response.data.current.temp;
        this.temperatures.fahrenheit = (response.data.current.temp - 273.15) * 9 / 5 + 32;
        this.temperatures.celsius = response.data.current.temp - 273.15;
        console.log(this.temperatures);
      })
    },
  },
};
</script>

<template>
  <div class="home">
    <!-- <h1>{{ message }}</h1>
    <h1>{{ this.current }}</h1> -->
    <h1>{{ this.current.weather[0].description.toUpperCase() }}</h1>
    <div id="icon">
      <img v-bind:src="this.icon">
    </div>
  </div>
</template>

<style>

</style>