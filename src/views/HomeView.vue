<script>
import axios from 'axios';

export default {
  data: function () {
    return {
      message: "Welcome to Vue.js!",
    };
  },
  created: function () { },
  methods: {
    getWeather: function () {
      axios.get("").then(response => {
        console.log(response)
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