# weather-app

This is a simple, single page weather app styled for mobile and built in VueJS. It leverages the Open Weather Map and
Google Place APIs to show the user the weather and forecast in their current location on page load.  The user can then
enter any city on Earth with Google Place Autocomplete and immediately see the current weather and forecast for that
location.  Upon selecting an autocomplete option, the app gets the geo coordinates returned from the Google API and uses
those coordinates to make a request to the Open Weather Map API.


## Rename the temporary env file and add your own API keys
```
mv .envtemp .env
```

## Project setup
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