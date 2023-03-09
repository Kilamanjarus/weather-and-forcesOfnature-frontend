<script>
import axios from 'axios';

export default {
  data: function () {
    return {
      message: "Welcome to Vue.js!",
      lat: 43,
      lon: -75,
      address: {},
      geocode_responses: {},
      response_address: "",
      map: null,
      imageUrl: '',
      daily: [],
      hourly: [],
      minutely: [],
      current: [],
      icon: "",
      current_description: "",
      temperatures: {},
      show_response: false,
      state_check: "",
      currentTime: null,
      currentDate: null,
      date_1: null,
      date_2: null,
      now: null,
      hours: "",
      minutes: "",
      seconds: "",
    }
  },
  created: function () {
  },
  mounted() {
    this.updateTime()
    setInterval(() => this.updateTime(), 1000)
  },
  methods: {
    getGeocode: function () {
      axios.get(`http://api.openweathermap.org/geo/1.0/direct?q=${this.address.city_name}&limit=3&appid=${process.env.VUE_APP_WEATHER_API}`).then(response => {
        console.log(response.data);
        this.geocode_responses = response.data;
        this.getWeather(response.data[0].lat, response.data[0].lon, response.data[0].name, response.data[0].state);
        console.log(this.geocode_responses);
      })
    },
    getWeather: function (lat, lon, name, state) {
      axios.get(`https://api.openweathermap.org/data/3.0/onecall?lat=${lat}&lon=${lon}&appid=${process.env.VUE_APP_WEATHER_API}`).then(response => {
        // console.log(response.data);
        this.daily = response.data.daily;
        this.hourly = response.data.hourly;
        this.minutely = response.data.minutely;
        this.message = response.data.alerts;
        this.current = response.data.current;
        this.current_description = this.toTitleCase(response.data.current.weather[0].description);
        this.response_address = name + ", " + state;
        this.state_check = state
        this.icon = `http://openweathermap.org/img/wn/${response.data.current.weather[0].icon}@2x.png`;
        this.temperatures.kelvin = Math.round(response.data.current.temp) + "\u00B0 K";
        this.temperatures.fahrenheit = Math.round((response.data.current.temp - 273.15) * 9 / 5 + 32) + "\u00B0 F";
        this.temperatures.celsius = Math.round(response.data.current.temp - 273.15) + "\u00B0 C";
        this.show_response = true;
        console.log(this.hourly);
      })
    },
    updateTime() {
      this.now = new Date()
      this.hours = this.now.getHours()
      this.minutes = this.now.getMinutes()
      this.seconds = this.now.getSeconds()
      this.currentTime = `${this.hours}:${this.minutes}`
      this.currentDate = `${this.now.getMonth() + 1}/${this.now.getDate()}`
      this.date_1 = `${this.now.getMonth() + 1}/${this.now.getDate() + 1}`
      this.date_2 = `${this.now.getMonth() + 1}/${this.now.getDate() + 2}`
      this.forecastDate = `${this.now.getMonth() + 1}/${this.now.getDate()}`
      console.log(this.currentTime);
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
    <h1>{{ this.currentDate }} {{ this.currentTime }}</h1>
    <button @click="getGeocode">Get Weather</button>
    <div></div>
    <div v-if="this.show_response">Choose Your Response</div>
    <span class="response_choice" v-for="response in this.geocode_responses">
      <button @click="this.getWeather(response.lat, response.lon, response.name, response.state)"
        v-if="response.state != this.state_check">{{ response.name }},
        {{
          response.state
        }}
      </button>&nbsp;
    </span>
    <h1>{{ this.response_address }}</h1>
    <h1>{{ this.temperatures.fahrenheit }}</h1>
    <!-- <h1>{{ message }}</h1>
    <h1>{{ this.current }}</h1> -->
    <h1></h1>
    <h1>{{ this.current_description }}</h1>
    <img v-bind:src="this.icon">
    <div></div>
    <h1 v-if="this.hourly != ``">Forecast:</h1>

    <!-- Forecast boxes -->
    <div v-for="forcast, index in this.hourly" class="box">

      <!-- Same Day AM -->
      <div class="box-header" v-if="this.hours + index < 12"><b>{{ this.currentDate }} {{
        this.hours + index
      }} AM
        </b>
        <img v-bind:src="`http://openweathermap.org/img/wn/${forcast.weather[0].icon}@2x.png`">
      </div>

      <!-- Same Day PM -->
      <div class="box-header" v-if="this.hours > 12 && this.hours + index < 24"><b>{{ this.currentDate }} {{
        this.hours +
          index - 12
      }}PM
        </b>
        <img v-bind:src="`http://openweathermap.org/img/wn/${forcast.weather[0].icon}@2x.png`">
      </div>

      <!-- Next Day Midnight -->
      <div class="box-header" v-if="this.hours + index == 24"><b>{{ this.date_1 }} 12 AM
        </b>
        <img v-bind:src="`http://openweathermap.org/img/wn/${forcast.weather[0].icon}@2x.png`">
      </div>

      <!-- Next Day AM -->
      <div class="box-header" v-if="this.hours + index > 24 && this.hours + index < 36"><b>{{ this.date_1 }} {{
        this.hours + index -
          24
      }} AM
        </b>
        <img v-bind:src="`http://openweathermap.org/img/wn/${forcast.weather[0].icon}@2x.png`">
      </div>

      <!-- Next Day Noon -->
      <div class="box-header" v-if="this.hours + index == 36"><b>{{ this.date_1 }} 12 PM
        </b>
        <img v-bind:src="`http://openweathermap.org/img/wn/${forcast.weather[0].icon}@2x.png`">
      </div>

      <!-- Next Day PM -->
      <div class="box-header" v-if="this.hours + index > 36 && this.hours + index < 48"><b>{{ this.date_1 }} {{
        this.hours +
          index - 36
      }}PM
        </b>
        <img v-bind:src="`http://openweathermap.org/img/wn/${forcast.weather[0].icon}@2x.png`">
      </div>

      <!-- 2 Day Midnight -->
      <div class="box-header" v-if="this.hours + index == 48"><b>{{ this.date_1 }} 12 AM
        </b>
        <img v-bind:src="`http://openweathermap.org/img/wn/${forcast.weather[0].icon}@2x.png`">
      </div>

      <!-- 2 Day AM -->
      <div class="box-header" v-if="this.hours + index > 48 && this.hours + index < 60"><b>{{ this.date_2 }} {{
        this.hours + index -
          48
      }} AM
        </b>
        <img v-bind:src="`http://openweathermap.org/img/wn/${forcast.weather[0].icon}@2x.png`">
      </div>

      <!-- 2 Day Midnight -->
      <div class="box-header" v-if="this.hours + index == 60"><b>{{ this.date_2 }} 12 AM
        </b>
        <img v-bind:src="`http://openweathermap.org/img/wn/${forcast.weather[0].icon}@2x.png`">
      </div>

      <!-- 2 Day PM -->
      <div class="box-header" v-if="this.hours + index > 60"><b>{{ this.date_1 }} {{
        this.hours +
          index - 60
      }}PM
        </b>
        <img v-bind:src="`http://openweathermap.org/img/wn/${forcast.weather[0].icon}@2x.png`">
      </div>

      <!-- Boxes for Windspeed/Humidity -->
      <div class="box-temp"><b>{{
        Math.round((forcast.temp - 273.15) * 9 / 5 + 32) +
          "\u00B0 F"
      }}</b></div>
      <div>
        {{ forcast.wind_speed }} MPH Wind
      </div>
      <div>
        {{ forcast.humidity }} % Humidity
      </div>
    </div>
  </div>
</template>

<style>
.box {
  display: inline-block;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 200px;
  height: 150px;
  background-color: #f5f5f5;
  border: 1px solid #ccc;
}

.box-header {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 50px;
  background-color: #ddd;
}

.box-time {
  font-size: 16px;
  font-weight: bold;
  margin-right: 10px;
}

.box-icon {
  width: 30px;
  height: 30px;
  background-color: #ccc;
  border-radius: 50%;
}

.box-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100px;
  padding: 10px;
}

.box-content-row {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 30px;
}

.box-content-label {
  font-size: 14px;
  font-weight: bold;
  margin-right: 10px;
}

.box-content-value {
  font-size: 14px;
}

.box-temp {
  font-size: 22px;
  font-weight: bold;
}
</style>