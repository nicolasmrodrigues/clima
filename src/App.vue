<script setup>
import { reactive } from 'vue'
import Preloader from './components/Preloader.vue'
import Form from './components/Form.vue'
import WeatherCard from './components/WeatherCard.vue';
import Graph from './components/Graph.vue';

const weatherapiKey = 'c5730d43197b4199a5d174227232306'
const ipKey = 'f2798494335c49ebb6cf5808f85c4a8f'
const ipBaseUrl = 'https://ipgeolocation.abstractapi.com/v1/?'
const forecastBaseUrl = 'https://api.weatherapi.com/v1/forecast.json?'
const iconBaseUrl = 'https://cdn.weatherapi.com/weather/128x128/'

const DaysOfTheWeek = ['Dom', 'Seg', 'Terç', 'Qua', 'Qui', 'Sex', 'Sáb']
const months = ['Jan', 'Fev', 'Mar', 'Abr', 'Maio', 'Jun', 'Jul', 'Ago', 'Set', 'Out', 'Nov', 'Dez']

const info = reactive({
  city: '',
  requestedCity: '',
  forecastMaxAndMinTemperatures: [
    {
      max: 0,
      min: 0
    },
    {
      max: 0,
      min: 0,
    },
    {
      max: 0,
      min: 0
    }
  ],
  currentHumidity: 0,
  currentTemperature: 0,
  currentRealFeel: 0,
  iconUrl: '',
  isReadyToShowUp: false,
  localDate: '',
  localDateFormatted: '',
  weatherDescription: '',
  windSpeed: 0
})

function createsLocalDateString() {
  let localDate = new Date(info.localDate)
  let localDateYear = localDate.getFullYear()
  let localDateMonth = months[localDate.getMonth()]
  let localDateDate = localDate.getDate()
  let localDateDay = DaysOfTheWeek[localDate.getDay()]
  let localDateHours = localDate.getHours()
  let localDateMinutes = localDate.getMinutes()

  if (localDateMinutes < 10) {
    localDateMinutes = `0${localDateMinutes}`
  }

  if (localDateHours === 0) {
    localDateHours = 12
    info.localDateFormatted += `${localDateHours}:${localDateMinutes} AM`
  } else if (localDateHours < 12) {
    info.localDateFormatted += `${localDateHours}:${localDateMinutes} AM`
  } else if (localDateHours === 12) {
    info.localDateFormatted += `${localDateHours}:${localDateMinutes} PM`
  } else if (localDateHours > 12) {
    info.localDateFormatted += `${localDateHours - 12}:${localDateMinutes} PM`
  }

  info.localDateFormatted += `, ${localDateDay}, ${localDateDate} ${localDateMonth}, ${localDateYear}`
}

function getWeatherInfo(data) {
  info.weatherDescription = data.current.condition.text
  info.localDate = data.location.localtime
  info.currentRealFeel = Math.round(data.current.feelslike_c)
  info.currentTemperature = Math.round(data.current.temp_c)
  info.windSpeed = Math.round(data.current.wind_kph)
  info.currentHumidity = data.current.humidity
  info.iconUrl = `${iconBaseUrl}${data.current.condition.icon.slice(35, data.current.condition.icon.length)}`
}

function createsUrls() {
  info.city = info.city.replace(' ', '%20')
  info.city = info.city.replace('ç', 'c')
  return [`${forecastBaseUrl}q=${info.city}&lang=pt&key=${weatherapiKey}`, `${forecastBaseUrl}q=${info.city}&lang=pt&days=3&key=${weatherapiKey}`]
}

function getForecastInfo(data) {
  let forecast = [...data.forecast.forecastday]

  for (let i = 0; i < forecast.length; i++) {
    info.forecastMaxAndMinTemperatures[i].max = Math.round(forecast[i].day.maxtemp_c)
    info.forecastMaxAndMinTemperatures[i].min = Math.round(forecast[i].day.mintemp_c)
  }
}

function getMaxAndMinTemperatures() {
  let maxTemperatures = []
  let minTemperatures = []

  for (let i = 0; i < info.forecastMaxAndMinTemperatures.length; i++) {
    maxTemperatures.push(info.forecastMaxAndMinTemperatures[i].max)
    minTemperatures.push(info.forecastMaxAndMinTemperatures[i].min)
  }
  return [maxTemperatures, minTemperatures]
}

function getNextDaysOfTheWeek() {
  const date = new Date(info.localDate)
  let addToIndex = 0
  const dayToday = DaysOfTheWeek[date.getDay()]
  let indexOfDayToday = DaysOfTheWeek.indexOf(dayToday)
  let nextDays = []

  for (let i = 0; i < 3; i++) {
    if (indexOfDayToday + addToIndex === 7) {
      indexOfDayToday = 0
      addToIndex = 0
    }
    nextDays.push(`${DaysOfTheWeek[indexOfDayToday + addToIndex]}.`)
    addToIndex += 1
  }

  return nextDays
}

function drawGraph() {
  const maxAndMinTemperatures = getMaxAndMinTemperatures()
  const graph = document.getElementById('graph')

  const xValues = getNextDaysOfTheWeek()
  const yMaxTemperatures = maxAndMinTemperatures[0]
  const yMinTemperatures = maxAndMinTemperatures[1]

  let maxTemperaturesTrace = {
    x: xValues,
    y: yMaxTemperatures,
    type: 'bar',
    name: 'Máxima',
    text: [`${yMaxTemperatures[0]}°C`, `${yMaxTemperatures[1]}°C`, `${yMaxTemperatures[2]}°C`],
    textposition: 'auto',
    hoverinfo: 'none',
    marker: {
      color: 'rgb(58,200,225)',
      line: {
        color: 'rgb(8,48,107)',
        width: 1.5
      }
    }
  }

  let minTemperaturesTrace = {
    x: xValues,
    y: yMinTemperatures,
    type: 'bar',
    name: 'Mínima',
    text: [`${yMinTemperatures[0]}°C`, `${yMinTemperatures[1]}°C`, `${yMinTemperatures[2]}°C`],
    textposition: 'auto',
    hoverinfo: 'none',
    opacity: 1,
    marker: {
      color: 'rgb(158,202,225)',
      line: {
        color: 'rgb(8,48,107)',
        width: 1.5
      }
    }
  }

  let data = [maxTemperaturesTrace, minTemperaturesTrace]

  let layout = {
    title: 'Temperatura máximas e mínimas',
    font: { size: 18 },
    barmode: 'group',
    width: 700,
    height: 410,
  }

  let config = { staticPlot: true, responsive: true }
  Plotly.newPlot(graph, data, layout, config)
}

function GetWeatherAndForecast() {
  info.isReadyToShowUp = false
  info.localDateFormatted = ''

  const urls = createsUrls()

  const currentWeatherUrl = urls[0]
  const forecastUrl = urls[1]

  fetch(currentWeatherUrl).then(response => {
    response.json().then(jsonData => {
      getWeatherInfo(jsonData)
      createsLocalDateString()
    })
  }).then(() => {
    fetch(forecastUrl).then(response => {
      response.json().then(jsonData => {
        getForecastInfo(jsonData)

        drawGraph()

        info.isReadyToShowUp = true
      })
    })
  })
}


function getCurrentLocationInfo() {
  const ipUrl = `${ipBaseUrl}api_key=${ipKey}`

  fetch(ipUrl).then(response => {
    response.json().then(jsonData => {
      info.city = info.requestedCity = jsonData.city
      let cityInput = document.getElementById('city-input')
      GetWeatherAndForecast()
      cityInput.value = jsonData.city
    })
  })
}

window.addEventListener("load", () => {
  getCurrentLocationInfo()
});

function changesCity(event) {
  info.city = event.target.value.charAt(0).toUpperCase() + event.target.value.slice(1)
}

function updatesRequestedCity() {
  info.requestedCity = info.city
}

</script>

<template>
  <div class="dashboard-container container mt-5 p-4 rounded-3">
    <Form :submit="GetWeatherAndForecast" :input-change="changesCity" :button-click="updatesRequestedCity" />
    <div class="preloader-container" v-show="!info.isReadyToShowUp">
      <Preloader />
    </div>
    <div class="forecast-container" v-show="info.isReadyToShowUp">
      <WeatherCard :info="info" />
      <Graph />
    </div>
  </div>
</template>

<style scoped lang="scss">
.dashboard-container {
  background-color: #fff;
  height: 80vh;
  box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;
}

.forecast-container {
  display: flex;
}

.preloader-container {
  width: 100%;
  margin-top: 10%;

}
</style>
