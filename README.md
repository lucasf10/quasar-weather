# Quasar Weather

A simple weather app with current location and search features.

## How to run this app

1. Create an account on [Open Weather Map](https://openweathermap.org/api) and copy your API key.

2. Paste your API key on quasar.conf.js:
```bash
build: {
  env: {
    OPEN_WEATHER_API_KEY: <YOUR API KEY>
  },
  ...
}
```

3. Install all dependencies with yarn:

```bash
yarn
```

4. Start app:

```bash
yarn quasar dev
```

5. Access:

```bash
http://localhost:8080
```

## Technologies used

- Vue.js
- Yarn
- Quasar
- Electron
- Cordova

## Features included

- Get weather data from current position
- Get weather data from a searched city
- Support for Web, Windows, MacOS, Android and iOS.