<script>
import axios from 'axios';

export default {
  data: function () {
    return {
      message: "Welcome to Vue.js!",
      lat: 43,
      lon: -75,
      address: {},
      response_address: "",
      map: null,
      imageUrl: '',
      daily: [],
      hourly: [],
      minutely: [],
      current: [],
      icon: "",
      current_description: "",
      temperatures: {}
    };
  },
  created: function () {
  },
  methods: {
    getGeocode: function () {
      axios.get(`http://api.openweathermap.org/geo/1.0/direct?q=${this.address.city_name}&limit=3&appid=${process.env.VUE_APP_WEATHER_API}`).then(response => {
        console.log(response.data);
        this.lat = response.data[0].lat;
        this.lon = response.data[0].lon;
        this.response_address = response.data[0].name + ", " + response.data[0].state;
        this.getWeather(response.data[0].lat, response.data[0].lon);
      })
    },
    getWeather: function (lat, lon) {
      axios.get(`https://api.openweathermap.org/data/3.0/onecall?lat=${lat}&lon=${lon}&appid=${process.env.VUE_APP_WEATHER_API}`).then(response => {
        // console.log(response.data);
        this.daily = response.data.daily;
        this.hourly = response.data.hourly;
        this.minutely = response.data.minutely;
        this.message = response.data.alerts;
        this.current = response.data.current;
        this.current_description = this.toTitleCase(response.data.current.weather[0].description);
        this.icon = `http://openweathermap.org/img/wn/${response.data.current.weather[0].icon}@2x.png`;
        this.temperatures.kelvin = Math.round(response.data.current.temp) + "\u00B0 K";
        this.temperatures.fahrenheit = Math.round((response.data.current.temp - 273.15) * 9 / 5 + 32) + "\u00B0 F";
        this.temperatures.celsius = Math.round(response.data.current.temp - 273.15) + "\u00B0 C";
        // console.log(this.current);
      })
    },
    getMap: function () {
      axios.get(`https://tile.openweathermap.org/map/temp_new/3/1/1?appid=${process.env.VUE_APP_WEATHER_API}`).then(response => {
        this.imageUrl = `https://tile.openweathermap.org/map/temp_new/5/4/4?appid=${process.env.VUE_APP_WEATHER_API}`;
        console.log(this.imageUrl);
      })
    },
    toTitleCase: function (str) {
      return str.replace(
        /\w*/g,
        function (txt) {
          return txt.charAt(0).toUpperCase() + txt.substr(1).toLowerCase();
        }
      );
    },
  },
};
</script>

<template>
  <div class="home">
    <input type="text" v-model="address.city_name" v-on:keyup.enter="getGeocode">
    <button @click="getGeocode">Get Weather</button>
    <h1>{{ this.response_address }}</h1>
    <h1>{{ this.temperatures.fahrenheit }}</h1>
    <!-- <h1>{{ message }}</h1>
    <h1>{{ this.current }}</h1> -->
    <h1></h1>
    <h1>{{ this.current_description }}</h1>
    <img v-bind:src="this.icon">
    <!-- <div id="temp_map">
      <img :src="this.imageUrl" alt="Map tile">
    </div> -->
  </div>
</template>

<style>

</style>