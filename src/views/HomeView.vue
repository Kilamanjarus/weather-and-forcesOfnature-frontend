<script>
import axios from 'axios';

export default {
  data: function () {
    return {
      message: "Welcome to Vue.js!",
      lat: 0,
      lon: 0,
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
      temperatures: { fahrenheit: "", celsius: "", kelvin: "", high: 0, low: 0 },
      show_response: false,
      state_check: "",

      currentTime: null,
      timeclock: "",
      currentDate: null,
      date_1: null,
      date_2: null,
      now: null,
      month: null,
      day: null,
      hours: "",
      minutes: "",
      seconds: "",

      backgroundImage: "",
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
        // console.log(response.data);
        this.geocode_responses = response.data;
        this.getWeather(response.data[0].lat, response.data[0].lon, response.data[0].name, response.data[0].state);
        // console.log(this.geocode_responses);
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
        this.setBackground(response.data.current.weather[0].icon)
        this.temperatures.kelvin = Math.round(response.data.current.temp) + "\u00B0 K";
        this.temperatures.fahrenheit = Math.round((response.data.current.temp - 273.15) * 9 / 5 + 32) + "\u00B0 F";
        this.temperatures.high = Math.round((response.data.current.temp - 273.15) * 9 / 5 + 32);
        this.temperatures.low = Math.round((response.data.current.temp - 273.15) * 9 / 5 + 32);
        this.temperatures.celsius = Math.round(response.data.current.temp - 273.15) + "\u00B0 C";
        this.show_response = true;
        // console.log(this.current);
        this.getHighLow()
      })
    },
    updateTime() {
      this.now = new Date()
      this.month = this.dateToMonth(this.now.getMonth() + 1)
      this.day = this.now.getDate()
      this.hours = this.now.getHours()
      this.minutes = this.now.getMinutes()
      this.seconds = this.now.getSeconds()
      this.currentTime = `${this.setHoursToAmPm(this.hours)}:${this.minutes}`
      this.currentDate = `${this.now.getMonth() + 1}/${this.now.getDate()}`
      this.date_1 = `${this.now.getMonth() + 1}/${this.now.getDate() + 1}`
      this.date_2 = `${this.now.getMonth() + 1}/${this.now.getDate() + 2}`
      this.forecastDate = `${this.now.getMonth() + 1}/${this.now.getDate()}`
      console.log(this.dateToMonth(this.now.getMonth() + 1));
    },
    getMap: function () {
      axios.get(`https://tile.openweathermap.org/map/temp_new/3/1/1?appid=${process.env.VUE_APP_WEATHER_API}`).then(response => {
        this.imageUrl = `https://tile.openweathermap.org/map/temp_new/5/4/4?appid=${process.env.VUE_APP_WEATHER_API}`;
        // console.log(this.imageUrl);
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
    dateToMonth: function (date) {
      if (date == 1) {
        return "January"
      }
      if (date == 2) {
        return "February"
      }
      if (date == 3) {
        return "March"
      }
      if (date == 4) {
        return "April"
      }
      if (date == 5) {
        return "May"
      }
      if (date == 6) {
        return "June"
      }
      if (date == 7) {
        return "July"
      }
      if (date == 8) {
        return "August"
      }
      if (date == 9) {
        return "September"
      }
      if (date == 10) {
        return "October"
      }
      if (date == 11) {
        return "November"
      }
      if (date == 12) {
        return "December"
      }
    },
    setHoursToAmPm: function (hours) {
      if (hours >= 12) {
        this.timeclock = "PM"
        return hours - 12
      }
      else {
        this.timeclock = "AM"
        return hours
      }
    },
    getHighLow: function () {
      console.log("Running getHighLow");
      this.hourly.forEach((element, index) => {
        if (this.hours + index < 24) {
          if (Math.round((element.temp - 273.15) * 9 / 5 + 32) > this.temperatures.high) {
            this.temperatures.high = Math.round((element.temp - 273.15) * 9 / 5 + 32)
            console.log(this.temperatures.high);
          }
          if (Math.round((element.temp - 273.15) * 9 / 5 + 32) < this.temperatures.low) {
            this.temperatures.low = Math.round((element.temp - 273.15) * 9 / 5 + 32)
            console.log(this.temperatures.high);
          }
        }
      })
    },
    setBackground(icon) {
      console.log(icon)
      switch (icon) {
        case '01d': //clear sky day
          this.backgroundImage = 'https://w0.peakpx.com/wallpaper/252/407/HD-wallpaper-a-clear-day-at-beach-rocks-sun-sunlights-background-clouds-cenario-sundown-nice-multicolor-scenario-bright-waterscape-brightness-cena-oceanscape-sky-trees-panorama-sunrays-cool.jpg'
          break;
        case '01n': //clear sky night
          this.backgroundImage = 'https://media.istockphoto.com/id/475374757/photo/star-in-blue-sky-night-time-scene.jpg?s=612x612&w=0&k=20&c=eFrDRX7X3tuV7QzRGLBEYrmaElMRn2PdAxasP64hDds='
          break;
        case '02d': //few clouds day
          this.backgroundImage = 'https://media.istockphoto.com/id/499680089/photo/light-blue-sky.jpg?s=612x612&w=0&k=20&c=Sxj_elMitmPyqcX0z1x7vJdVyT_HZhmYdqLf63uEcgA='
          break;
        case '02n': //few clouds night
          this.backgroundImage = `https://images-wixmp-ed30a86b8c4ca887773594c2.wixmp.com/f/fc470c8c-2e66-48d5-af87-a0a6818fc06d/dext91s-a57bd8b0-646b-46a0-9315-0bcc45921975.png/v1/fill/w_1057,h_756,q_70,strp/itchi_by_raikonkitsune_dext91s-pre.jpg?token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJ1cm46YXBwOjdlMGQxODg5ODIyNjQzNzNhNWYwZDQxNWVhMGQyNmUwIiwiaXNzIjoidXJuOmFwcDo3ZTBkMTg4OTgyMjY0MzczYTVmMGQ0MTVlYTBkMjZlMCIsIm9iaiI6W1t7ImhlaWdodCI6Ijw9OTE2IiwicGF0aCI6IlwvZlwvZmM0NzBjOGMtMmU2Ni00OGQ1LWFmODctYTBhNjgxOGZjMDZkXC9kZXh0OTFzLWE1N2JkOGIwLTY0NmItNDZhMC05MzE1LTBiY2M0NTkyMTk3NS5wbmciLCJ3aWR0aCI6Ijw9MTI4MCJ9XV0sImF1ZCI6WyJ1cm46c2VydmljZTppbWFnZS5vcGVyYXRpb25zIl19.XTqFWHMwmYtKD1GffwbuLkgxYUo1sXI0bCfoLEt5eHM`
          break;
        case '03d': //scattered clouds day
          this.backgroundImage = 'https://media.istockphoto.com/id/171225633/photo/deep-blue-view-on-a-lightly-clouded-day.jpg?s=612x612&w=0&k=20&c=KGV9ieDdP5wgx9unc_HwHmP5wuRmpgyDSA0h-3_gNNo='
          break;
        case '03n': //scattered clouds night
          this.backgroundImage = 'https://rare-gallery.com/thumbs/879731-Sky-Mountains-Forests-Night-Moon-Clouds.jpg'
          break;
        case '04d': //broken clouds day
          this.backgroundImage = `https://pm1.narvii.com/6922/075acd11e139fac05e46c33361a879403a8b6ccer1-2048-1152v2_hq.jpg`
          break;
        case '04n': //broken clouds night
          this.backgroundImage = 'https://media.istockphoto.com/id/1154430991/photo/dark-blue-storm-cloudy-sky-background.jpg?b=1&s=170667a&w=0&k=20&c=nyurCBs4SGGjny4MRhJdb4aQXc9MS1pj5VCw1XV4uKA='
          break;
        case '10d': //rain day
          this.backgroundImage = 'https://images.pexels.com/photos/2259232/pexels-photo-2259232.jpeg';
          break;
        case '10n': //rain night
          break;
        case '11d': //thunderstorm day
          break;
        case '11n': //thunderstorm night
          break;
        case '13d': //snow day
          break;
        case '13n': //snow night
          break;
        case '50d': //mist day
          break;
        case '50n': //mist night
          break;
        case '09d': //shower rain day
          this.backgroundImage = 'https://gray-ksla-prod.cdn.arcpublishing.com/resizer/HLiMBZHQq33LQDqoaInn7xFbv5M=/1200x675/smart/filters:quality(85)/cloudfront-us-east-1.images.arcpublishing.com/gray/JFDZ7U577FFBTCTSLUKDQDSAYQ.png';
          break;
        case '09n': //shower rain night
          break;
      }
    },
  },
}
</script>

<template>
  <div :style="{ backgroundImage: `url(${backgroundImage})` }" class="my-background">
    <div>
      <!-- Time Bar -->
      <div class="text-response">
        <h1>{{ this.month }} {{ this.day }} {{ this.currentTime }} {{ this.timeclock }}</h1>
        <div></div>
        <!-- City Name Search Input -->
        <input type="text" v-model="address.city_name" placeholder="City Name..." v-on:keyup.enter="getGeocode"
          class="text-in-response">
        <!-- Button For Search -->
        <button @click="getGeocode" class="text-in-response">Get Weather</button>
        <br>
        <!-- Buttons to change Response States -->
        <p v-if="this.show_response" class="text-in-response"><b>Choose Your Response </b></p>
        <span class="response_choice" v-for="response in this.geocode_responses">
          <button class="text-in-response"
            @click="this.getWeather(response.lat, response.lon, response.name, response.state)"
            v-if="response.state != this.state_check">{{ response.name }},
            {{
              response.state
            }}
          </button>&nbsp;
        </span>
        <!-- Current City Response -->
        <h1>{{ this.response_address }}</h1>
        <h1 v-if="this.temperatures.fahrenheit != ``">Current Temperature: {{ this.temperatures.fahrenheit }}
        </h1>
        <h1 v-if="this.temperatures.fahrenheit != ``">Today's Current High: {{ this.temperatures.high + "\u00B0 F" }} /
          Today's Current Low:
          {{
            this.temperatures.low + "\u00B0 F"
          }}</h1>
        <h1 h1 v-if="this.temperatures.fahrenheit != ``">Wind Speeds of {{ this.current.wind_speed }} MPH</h1>
        <h1 h1 v-if="this.temperatures.fahrenheit != ``">Humidity of {{ this.current.humidity }}%</h1>
        <!-- <h1>{{ message }}</h1>
    <h1>{{ this.current }}</h1> -->
        <h1></h1>
        <h1>{{ this.current_description }}</h1>
        <img v-bind:src="this.icon">
        <div></div>
        <h1 v-if="this.hourly != ``">Forecast:</h1>
      </div>
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
        <div class="box-header" v-if="this.hours + index > 12 && this.hours + index < 24"><b>{{ this.currentDate }} {{
          this.hours +
            index - 12
        }}PM
          </b>
          <img v-bind:src="`http://openweathermap.org/img/wn/${forcast.weather[0].icon}@2x.png`">
        </div>

        <!-- Same Day Noon -->
        <div class="box-header" v-if="this.hours + index == 12"><b>{{ this.currentDate }} 12 PM
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
  </div>
</template>

<style>
.box {
  display: inline-block;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 15em;
  height: 10em;
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
  font-size: 20px;
  font-weight: bold;
  margin-right: 10px;
}

.box-content-value {
  font-size: 20px;
}

.box-temp {
  font-size: 22px;
  font-weight: bold;
}

.my-background {
  background-repeat: no-repeat;
  background-size: cover;
  border-radius: 1em;
  border-color: black;
}

.text-in-response {
  font-size: 22px;
}

.response-choice {
  font-size: 22px;
}

.text-response {
  background-color: rgba(255, 255, 255, 0.3);
  max-width: 900px;
  margin: 0 auto;
  text-align: center;
  border-radius: 100px;
  border-color: black;
}
</style>