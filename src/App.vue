<template>
  <div id="app">
    <div class="row">
      <input ref="autocomplete"
        placeholder="Enter a City"
        class="search-location"
        onfocus="value = ''"
        type="text" />
    </div>
    <h3 class="top-padding">{{ focusedWeather.main }}</h3>
    <img v-if="focusedWeather.icon !== ''" class="icon-style" :src="getImgUrl(focusedWeather.icon)">



  </div>
</template>

<script>
export default {

  data: function(){
    return{
      focusedWeather: {
        temp: '',
        main: '',
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
      this.focusedWeather.temp = data.current.temp;
      this.focusedWeather.main = data.current.weather[0].main;
      this.focusedWeather.description = data.current.weather[0].description;
      //capitalize the words in description
      this.focusedWeather.description = this.focusedWeather.description.split(' ').map(word => word.charAt(0).toUpperCase() + word.substring(1)).join(' ');
      this.focusedWeather.icon = 'Rain';
      console.log(this.focusedWeather);
    },

    getImgUrl(pic) {
      return require('./assets/icons/'+pic+'.svg');
    },

    getIconName(icon){
      switch(icon){
        case '01d':
          return '';
        case '01n':
          return '';
        case '02d':
          return '';
        case '02n':
          return '';
        case '03d':
          return '';
        case '03n':
          return '';
        case '04d':
          return '';
        case '04n':
          return '';
        case '09d':
          return '';
        case '09n':
          return '';
        case '10d':
          return '';
        case '10n':
          return '';
        case '11d':
          return '';
        case '11n':
          return '';
        case '13d':
          return '';
        case '13n':
          return '';
        case '50d':
          return '';
        case '50n':
          return '';

        default:
          return '';
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
  padding-top: 10px;
}

.icon-style{
  width: 200px;
  height: 200px;

}
</style>