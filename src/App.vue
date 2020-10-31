<template>
  <div id="app">
    <div class="row">
      <input ref="autocomplete"
        placeholder="Enter a City"
        class="search-location"
        onfocus="value = ''"
        type="text" />
    </div>



    <h3 class="top-padding">{{ focusedWeather.city }}</h3>
    <h2>{{ focusedWeather.description }}</h2>


    <div class="row info-row" v-if="focusedWeather.temp !== ''">
      <div class="col-4">
        <h3 class="temp-styling">{{ focusedWeather.temp }}&#176;F</h3>
      </div>
      <div class="col-8">
        <h3 class="no-bottom-margin right-float">
          <span>{{ focusedWeather.wind }} mph </span>
          <img class="small-icon" src="./assets/icons/Windy.svg">
        </h3>
      </div>
    </div>


    <img v-if="focusedWeather.icon !== ''" class="icon-style" :src="getImgUrl(focusedWeather.icon)">



  </div>
</template>

<script>
export default {

  data: function(){
    return{
      focusedWeather: {
        temp: '',
        wind: '',
        description: '',
        city: '',
        icon: ''
      },
      imgString: 'Rain'
    }
  },

  mounted() {

    this.autocomplete = new google.maps.places.Autocomplete(
      (this.$refs.autocomplete),
      {types: ['geocode']}
    );

    this.autocomplete.addListener('place_changed', () => {
      let place = this.autocomplete.getPlace();
      let ac = place.address_components;
      let lat = place.geometry.location.lat();
      let lon = place.geometry.location.lng();
      this.focusedWeather.city = ac[0]["short_name"];

      console.log(`The user picked ${this.focusedWeather.city} with the coordinates ${lat}, ${lon}`);
      this.getWeather(lat, lon);
    });


    navigator.geolocation.getCurrentPosition(
       position => {
          console.log(position.coords.latitude);
          console.log(position.coords.longitude);
          this.getCityFrom(position.coords.latitude, position.coords.longitude);
       },
       error => {
          console.log(error.message);
       },
    )



  },

  methods: {

    async getWeather(lat, lon){
      console.log(lat);
      console.log(lon);

      let request = 'http://api.openweathermap.org/data/2.5/onecall?lat='+lat+'&lon='+lon+
        '&exclude=minutely,hourly,alerts&appid='+process.env.VUE_APP_WEATHER+'&units=imperial';

      const res = await fetch(request);
      const data = await res.json();
      console.log(data);
      this.focusedWeather.temp = Math.round(data.current.temp);
      this.focusedWeather.wind = Math.round(data.current.wind_speed);
      this.focusedWeather.description = data.current.weather[0].description;
      //capitalize the words in description
      this.focusedWeather.description = this.focusedWeather.description.split(' ').map(word => word.charAt(0).toUpperCase() + word.substring(1)).join(' ');
      this.focusedWeather.icon = this.getIconName(data.current.weather[0].icon);
      console.log(this.focusedWeather);
    },

    async getCityFrom(lat, long) {
      try {
        const request = "https://maps.googleapis.com/maps/api/geocode/json?latlng=" +
            lat + "," + long + "&result_type=locality&key=" + process.env.VUE_APP_MAPS
        const res = await fetch(request);
        const data = await res.json();

        if(data.error_message) {
          console.log(data.error_message)
        } else {
          console.log(data.results[0]);
          this.focusedWeather.city = data.results[0].address_components[0].short_name;
          this.getWeather(lat, long);

        }
      } catch (error) {
        console.log(error.message);
      }
    },

    getImgUrl(pic) {
      return require('./assets/icons/'+pic+'.svg');
    },

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
}

.search-location {
  display: block;
  width: 100%;
  margin: 0 auto;
  margin-top: 5vw;
  font-size: 20px;
  font-weight: 400;
  outline: none;
  height: 30px;
  line-height: 30px;
  text-align: center;
  border-radius: 10px;
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

.temp-styling{
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
  margin-bottom: 15px;
}

.right-float{
  float: right;
}

</style>