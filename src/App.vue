<template>
  <div id="app">
    <input ref="autocomplete"
        placeholder="Enter a City"
        class="search-location"
        onfocus="value = ''"
        type="text" />
  </div>
</template>

<script>
export default {
  mounted() {

    this.autocomplete = new google.maps.places.Autocomplete(
      (this.$refs.autocomplete),
      {types: ['geocode']}
    );

    this.autocomplete.addListener('place_changed', () => {
      let place = this.autocomplete.getPlace();
      console.log(place);
      let ac = place.address_components;
      let lat = place.geometry.location.lat();
      let lon = place.geometry.location.lng();
      let city = ac[0]["short_name"];

      console.log(`The user picked ${city} with the coordinates ${lat}, ${lon}`);
      this.getWeather(lat, lon);

    });
  },

  methods: {
    async getWeather(lat, lon){
      console.log(lat);
      console.log(lon);
      let request = 'http://api.openweathermap.org/data/2.5/weather?lat='+lat+'&lon='+lon+'&appid=32cf0aa06f9e43913c0913e7ead76070&units=imperial';
      const res = await fetch(request);
      const data = await res.json();
      console.log(data);
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
  background-color: #dcdde1;
  color: #2f3640;
  padding: 3rem;
}

.search-location {
  display: block;
  width: 60vw;
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
</style>