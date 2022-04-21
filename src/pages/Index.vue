<template>
  <q-page class="flex column" :class="bgClass">
    <div class="col q-pt-lg q-px-md">
      <q-input
        v-model="search"
        placeholder="Search"
        dark
        borderless
        @keyup.enter="() => getWeather()"
      >
        <template v-slot:before>
          <q-icon
            @click="getLocation"
            name="my_location"
          />
        </template>

        <template v-slot:append>
          <q-btn
            @click="() => getWeather()"
            round
            dense
            flat
            icon="search"
          />
        </template>
      </q-input>
    </div>
    
    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">
          {{ weatherData.name }}
        </div>

        <div class="text-h6 text-weight-light">
          {{ weatherData.weather[0].main }}
        </div>

        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          <span>{{ Math.round(weatherData.main.temp) }}</span>
          <span class="text-h4 relative-position degree">&deg;</span>
        </div>
      </div>

      <div class="col text-center">
        <img :src="`https:/openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`" alt="Bill">
      </div>
    </template>

    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">
          Quasar<br />Weather
        </div>

        <q-btn
          flat
          class="col"
          @click="getLocation"
        >
          <q-icon left size="3em" name="my_location" />
          <div>Find my location</div>
        </q-btn>
      </div>
    </template>

    <div class="col skyline"></div>
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data() {
    return {
      search: '',
      weatherData: null,
      lat: null,
      lon: null,
      apiUrl: 'https://api.openweathermap.org/data/2.5/weather',
    }
  },
  computed: {
    bgClass () {
      if (this.weatherData)
        return this.weatherData.weather[0].icon.endsWith('n') ? 'bg-night' : 'bg-day'
    }
  },
  methods: {
    getLocation() {
      this.$q.loading.show()

      if (this.$q.platform.is.electron) {
        this.$axios.get('https://freegeoip.app/json').then(response => {
          this.lat = response.data.latitude
          this.lon = response.data.longitude

          this.getWeather(true)
        })
      } else {
        navigator.geolocation.getCurrentPosition(postion => {
          this.lat = postion.coords.latitude
          this.lon = postion.coords.longitude

          this.getWeather(true)
        }, (err) => {
          this.handleError('Location not granted.')
        })
      }
    },
    async getWeather(byCurrentLocation = false) {
      this.$q.loading.show()
      try {
        const query = byCurrentLocation ? `lat=${this.lat}&lon=${this.lon}` : `q=${this.search}`
        const { data } = await this.$axios(`${this.apiUrl}?${query}&appid=${process.env.OPEN_WEATHER_API_KEY}&units=metric`)
        this.setWeatherData(data)
      } catch (err) {
        this.handleError('City not found.')
      }
      this.search = ''
    },
    setWeatherData(data) {
      this.weatherData = data
      this.$q.loading.hide()
    },
    handleError(errMsg) {
      this.$q.loading.hide()
      this.$q.notify({
        message: errMsg,
        icon: 'warning',
        iconSize: '20px',
        position: 'top',
        actions: [
          { label: 'Dismiss', color: 'white' }
        ]
      })
    }
  }
}
</script>

<style lang="sass">
  .q-page
    background: linear-gradient(to bottom, #136a8a, #267871)

    &.bg-night
      background: linear-gradient(to bottom, #232526, #414345)

    &.bg-day
      background: linear-gradient(to bottom, #00b4db, #0083b0)

  .degree
    top: -44px

  .skyline
    flex: 0 0 100px
    background-image: url('../assets/skyline.png')
    background-size: contain
    background-position: center bottom
</style>