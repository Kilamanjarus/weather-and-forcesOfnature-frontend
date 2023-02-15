<script>
import axios from 'axios';

export default {
  data: function () {
    return {
      message: "Welcome to Vue.js!",
      lat: 42.8667,
      lon: 88.3334,
      address: { city_name: 'New York', state_code: 'NY', country_code: 'US' },
      map: null,
      imageUrl: '',
    };
  },
  mounted: function () {
    const layer = 'temp_new';
    const zoom = 5;
    const x = 4;
    const y = 4;
    const url = `https://tile.openweathermap.org/map/${layer}/${zoom}/${x}/${y}?appid=${process.env.VUE_APP_WEATHER_API}`;
    axios.get(url, { responseType: 'blob' })
      .then(response => {
        this.mapTileUrl = URL.createObjectURL(response.data);
        console.log(this.mapTileUrl);
      })
      .catch(error => {
        console.error(error);
      });
  },
  created: function () {
    this.getWeather();
    this.getGeocode();
    // this.getMap();
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
        console.log(response);
        this.message = response.data;
      })
    },
    // getMap: function () {
    //   axios.get(`https://tile.openweathermap.org/map/temp_new/5/4/4?appid=${process.env.VUE_APP_WEATHER_API}`).then(response => {
    //     this.imageUrl = URL.createObjectURL(new Blob([response.data]));
    //     console.log(this.imageUrl);
    //   })
    // }
  },
};
</script>

<template>
  <div class="home">
    <h1>{{ message }}</h1>
  </div>
  <div>
    <img :src="mapTileUrl" alt="Map tile">
  </div>
</template>

<style>

</style>