# weather-app

This is a simple, single page weather app styled for mobile and built in VueJS. It leverages the Open Weather Map and
Google Place APIs to show the user the weather and forecast for their current location on page load. The user can then
enter any city on Earth with Google Place Autocomplete and immediately see the current weather and 7-day forecast for that
location. Upon selecting an autocomplete option, the app gets the geo coordinates returned from the Google API and uses
those coordinates to make a request to the Open Weather Map API. The forecasts at the bottom of the page are horizontally
scrollable!

![Alt text](./src/assets/examples/minneapolis.PNG?raw=true "Minneapolis")
![Alt text](./src/assets/examples/winnipeg.PNG?raw=true "Winnipeg")

## Local setup instructions:

## Clone the repository
```
git clone https://github.com/Sneez/weather-app.git
```

## Rename the temporary env file and add your own API keys
```
mv .envtemp .env
```

## Install dependencies
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### To view in browser:

- Navigate to localhost:8080
- Click 'Toggle device toolbar' button in Chrome inspector to view for mobile
- Select 'Allow' when asked to use your location