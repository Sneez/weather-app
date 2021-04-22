<template>
  <div id="app">

    <!-- search bar -->
    <div class="row">
      <input ref="autocomplete"
        placeholder="Enter a City"
        class="search-location"
        onfocus="value = ''"
        type="text" />
    </div>

    <div v-if="loaded">

      <!-- city and weather description -->
      <h4 class="top-padding">{{ city }}</h4>
      <h2>{{ focusedWeather.description }}</h2>

      <!-- info bar with temp and wind -->
      <div class="row info-row" v-if="focusedWeather.temp !== ''">
        <div class="col-4">
          <h3 class="gold-color">{{ focusedWeather.temp }}&#176;F</h3>
        </div>
        <div class="col-8">
          <h3 class="no-bottom-margin right-float">
            <span>{{ focusedWeather.wind }} mph </span>
            <img class="small-icon" src="../assets/icons/Windy.svg">
          </h3>
        </div>
      </div>

      <!-- weather icon -->
      <img v-if="focusedWeather.icon !== ''" class="icon-style" :src="getImgUrl(focusedWeather.icon)">

      <!-- forecast divs -->
      <div class="row">
        <div class="forecast-container">
          <div v-for="day in forecast" :key="day.date" class="forecast">
            <div>{{day.date}}</div>
            <div class="gold-color">{{day.temp}}&#176;F</div>
            <img class="forecast-icon" :src="getImgUrl(day.icon)">
            <div class="small-text">{{day.wind}} mph</div>
          </div>
        </div>
      </div>

    </div>
    <div v-if="!loaded" class="loader"></div>
  </div>
</template>

<script>
export default {
  name: 'Weather',
  data: function(){
    return{
      focusedWeather: {
        temp: '',
        wind: '',
        description: '',
        icon: '',
        date: ''
      },
      city: '',
      forecast: [],
      loaded: false
    }
  },

  mounted() {

    // init autocomplete for the input
    this.autocomplete = new google.maps.places.Autocomplete(
      (this.$refs.autocomplete),
      {types: ['geocode']}
    );

    // listener for setting current city and weather after autocomplete is clicked
    this.autocomplete.addListener('place_changed', () => {
      let place = this.autocomplete.getPlace();
      let ac = place.address_components;
      let lat = place.geometry.location.lat();
      let lon = place.geometry.location.lng();
      this.city = ac[0]["short_name"];
      this.getWeather(lat, lon);
    });

    // grab users current coordinates, grab their city, then get weather
    navigator.geolocation.getCurrentPosition(
       position => {
          let lat = position.coords.latitude;
          let lon = position.coords.longitude;
          this.getCityFrom(lat, lon);
          this.getWeather(lat, lon);
       },
       error => {
          console.log(error.message);
       },
    );

  },

  methods: {

    // get weather of selected location and set it for display
    async getWeather(lat, lon){

      try{
        this.loaded = false;
        let request = 'http://api.openweathermap.org/data/2.5/onecall?lat='+lat+'&lon='+lon+
          '&exclude=minutely,hourly,alerts&appid='+process.env.VUE_APP_WEATHER+'&units=imperial';
        const res = await fetch(request);
        const data = await res.json();

        if(data.message){
          console.log(data.message);
        } else {
          // set focused weather data
          this.focusedWeather = {
            temp: Math.round(data.current.temp),
            wind: Math.round(data.current.wind_speed),
            date: this.convertDate(data.current.dt),
            description: this.capitalizeWords(data.current.weather[0].description),
            icon: this.getIconName(data.current.weather[0].icon)
          }

          // set forecast array
          this.forecast = data.daily.map((day) => {
            return {
              temp: Math.round(day.temp.day),
              wind: Math.round(day.wind_speed),
              icon: this.getIconName(day.weather[0].icon),
              date: this.convertDate(day.dt),
            };
          });

          this.loaded = true;
        }
      } catch (error) {
        console.log(error.message)
      }
    },

    // grab users city from their geo coordinates
    async getCityFrom(lat, long) {
      try {
        const request = "https://maps.googleapis.com/maps/api/geocode/json?latlng=" +
            lat + "," + long + "&result_type=locality&key=" + process.env.VUE_APP_MAPS;
        const res = await fetch(request);
        const data = await res.json();

        if(data.error_message) {
          console.log(data.error_message)
        } else {
          // set current city
          this.city = data.results[0].address_components[0].short_name;
        }
      } catch (error) {
        console.log(error.message);
      }
    },

    // needed for dynamic img source to work
    getImgUrl(pic) {
      return require('../assets/icons/'+pic+'.svg');
    },

    // convert to dd/mm
    convertDate(epoch){
      let selectedDate = new Date(epoch * 1000);
      const options = { day: 'numeric', month: 'numeric' };
      return selectedDate.toLocaleDateString("en-US", options);
    },

    // capitalize the words in the weather description
    capitalizeWords(sentence){
      return sentence.split(' ')
        .map(word => word.charAt(0).toUpperCase() + word.substring(1)).join(' ');
    },

    // set icon based on the icon name returned from weather data
    getIconName(icon){
      switch(icon){
        case '01d':
          return 'Sun';
        case '01n':
          return 'ClearNight';
        case '02d':
          return 'SunAndCloud';
        case '02n':
          return 'CloudyNight';
        case '03d':
          return 'CloudAndSun';
        case '03n':
          return 'CloudyNight';
        case '04d':
          return 'CloudAndSun';
        case '04n':
          return 'CloudyNight';
        case '09d':
          return 'SunShower';
        case '09n':
          return 'Shower';
        case '10d':
          return 'Rain';
        case '10n':
          return 'Rain';
        case '11d':
          return 'Lightning';
        case '11n':
          return 'Lightning';
        case '13d':
          return 'Snow';
        case '13n':
          return 'Snow';
        case '50d':
          return 'Fog';
        case '50n':
          return 'Fog';
        default:
          return 'Windy';
      }
    }

  }
}
</script>

<style>
*, *::after, *::before {
  margin: 0;
  padding: 0;
  box-sizing: inherit;
}

body {
  background-color: #0a0840 !important;
  color: #fff !important;
  padding: 3rem;
  font-family: Raleway, serif !important;
}

.search-location {
  display: block;
  width: 100%;
  margin: 0 auto;
  font-size: 20px;
  font-weight: 400;
  outline: none;
  height: 30px;
  line-height: 30px;
  text-align: center;
  border-radius: 8px;
}

.top-padding{
  padding-top: 20px;
}

.icon-style{
  width: 80%;
  max-width: 400px;
  display: block;
  margin-left: auto;
  margin-right: auto;
  margin-bottom: 10px;
}

.no-bottom-margin{
  margin-bottom: 0px;
}

.small-icon{
  width: 45px;
  margin-left: -5px;
  margin-top: -5px;
  margin-right: -5px;
}

.gold-color{
  color: #ffcc33;
}

.info-row{
  border: 2px solid #697b94;
  padding-top: 5px;
  border-radius: 11px;
  margin-top: 15px;
  background-color: #3b3a66;
  margin-right: 0px !important;
  margin-left: 0px !important;
  margin-bottom: 10px;
}

.right-float{
  float: right;
}

.forecast-container{
  overflow: auto;
  width: 100%;
  height: 100%;
  white-space: nowrap;
  text-align: center;
}

.forecast{
  display: inline-block;
  border: 2px solid #697b94;
  border-radius: 7px;
  margin-right: 7px;
  font-size: 20px;
  text-align: center;
  width: 70px;
}

.forecast-icon{
  width: 50px;
}

.small-text{
  font-size: 18px;
}

.loader {
  border: 5px solid #f3f3f3;
  -webkit-animation: spin 1s linear infinite;
  animation: spin 1s linear infinite;
  border-top: 5px solid #555;
  border-radius: 50%;
  width: 200px;
  height: 200px;
  margin-top: 100px;
  display: block;
  margin-right: auto;
  margin-left: auto;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

#app{
  max-width: 800px;
  display: block;
  margin: auto;
}
</style>
