<script setup>
import { reactive } from 'vue'
import Formulario from './components/Formulario.vue'
import Preloader from './components/Preloader.vue'

const apiKey = 'c5730d43197b4199a5d174227232306'
const curretWeatherBaseUrl = 'https://api.weatherapi.com/v1/current.json?'
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

function getCityData() {
  info.isReadyToShowUp = false
  info.city = info.city.replace(' ', '%20')
  info.city = info.city.replace('ç', 'c')
  const curretWeatherUrl = `${curretWeatherBaseUrl}q=${info.city}&lang=pt&key=${apiKey}`
  info.localDateFormatted = ''
  fetch(curretWeatherUrl).then(response => {
    response.json().then(jsonData => {
      info.weatherDescription = jsonData.current.condition.text
      info.localDate = jsonData.location.localtime
      info.currentRealFeel = Math.round(jsonData.current.feelslike_c)
      info.currentTemperature = Math.round(jsonData.current.temp_c)
      info.windSpeed = Math.round(jsonData.current.wind_kph)
      info.currentHumidity = jsonData.current.humidity
      info.iconUrl = `${iconBaseUrl}${jsonData.current.condition.icon.slice(35, jsonData.current.condition.icon.length)}`

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
    })
  })

  const forecastUrl = `${forecastBaseUrl}q=${info.city}&lang=pt&days=3&key=${apiKey}`

  fetch(forecastUrl).then(response => {
    response.json().then(jsonData => {
      let forecastjsonData = jsonData.forecast.forecastday
      let forecast = [...forecastjsonData]

      for (let i = 0; i < forecast.length; i++) {
        info.forecastMaxAndMinTemperatures[i].max = Math.round(forecast[i].day.maxtemp_c)
        info.forecastMaxAndMinTemperatures[i].min = Math.round(forecast[i].day.mintemp_c)
      }

      drawGraph()
      info.isReadyToShowUp = true
    })
  })
}

function drawGraph() {
  const date = new Date(info.localDate)
  let addToIndex = 0
  const graph = document.getElementById('graph')
  const dayToday = DaysOfTheWeek[date.getDay()]
  let indexOfDayToday = DaysOfTheWeek.indexOf(dayToday)

  let xValues = []
  let yMinValues = []
  let yMaxValues = []

  for (let i = 0; i < info.forecastMaxAndMinTemperatures.length; i++) {
    yMaxValues.push(info.forecastMaxAndMinTemperatures[i].max)
  }

  for (let i = 0; i < 3; i++) {
    if (indexOfDayToday + addToIndex === 7) {
      indexOfDayToday = 0
      addToIndex = 0
    }
    xValues.push(`${DaysOfTheWeek[indexOfDayToday + addToIndex]}.`)
    addToIndex += 1
  }

  let trace1 = {
    x: xValues,
    y: yMaxValues,
    type: 'bar',
    name: 'Máxima',
    text: [`${yMaxValues[0]}°C`, `${yMaxValues[1]}°C`, `${yMaxValues[2]}°C`],
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

  for (let i = 0; i < info.forecastMaxAndMinTemperatures.length; i++) {
    yMinValues.push(info.forecastMaxAndMinTemperatures[i].min)
  }

  let trace2 = {
    x: xValues,
    y: yMinValues,
    type: 'bar',
    name: 'Mínima',
    text: [`${yMinValues[0]}°C`, `${yMinValues[1]}°C`, `${yMinValues[2]}°C`],
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

  let data = [trace1, trace2]

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


function location() {
  fetch('https://api.ipify.org?format=json')
    .then(x => x.json())
    .then(({ ip }) => {
      info.city = ip
      let url = `${curretWeatherBaseUrl}q=${info.city}&lang=pt&key=${apiKey}`
      let cityInput = document.getElementById('city-input')
      fetch(url).then(response => {
        response.json().then(jsonData => {
          info.city = jsonData.location.name
          info.requestedCity = info.city
          cityInput.value = info.city
        })
      })
      getCityData()
    });
}
window.addEventListener("load", function (event) {
  location()
});

</script>

<template>
  <div class="container mt-5 ps-4 rounded-3">
    <Formulario :updates-city="event => info.city = event.target.value"
      :changesRequestedCity="() => info.requestedCity = info.city.charAt(0).toUpperCase() + info.city.slice(1)"
      :get-data="getCityData" />
    <div class="forecast-container d-flex">
      {{ info.isReadyToShowUp }}
      <div class="col-md-4 weather-card d-inline-block mt-4" v-if="info.isReadyToShowUp">
        <div class="row">
          <div class="col-md-12 text-center">
            <span class="d-block" v-if="info.localDateFormatted">Horário de {{ info.requestedCity }}: </span>
            <span v-if="info.localDateFormatted">{{ info.localDateFormatted }}</span>
          </div>
        </div>
        <div class="row">
          <div class="col-md-12 text-center">
            <img :src="info.iconUrl" alt="">
          </div>
        </div>
        <div class="row mt-1 mb-3">
          <div class="col-md-12 text-center fw-semibold fs-4">
            {{ info.weatherDescription }}
          </div>
        </div>
        <div class="row ps-4">
          <div class="col-md-6 text-center fw-semibold fs-2 pe-0">
            <span class="fw-semibold fs-5 label d-block">Humidade</span>{{ info.currentHumidity }}%
          </div>
          <div class="col-md-6 text-center ps-0">
            <span class="fw-semibold fs-5 label d-block text-start">Velocidade do vento</span>
            <span class="fw-semibold fs-2 text-center pe-5">{{ info.windSpeed }} km/h</span>
          </div>
        </div>
        <div class="row ps-4 mt-4">
          <div class="col-md-6 text-center fw-semibold fs-2 pe-0">
            <span class="fw-semibold fs-5 label d-block">Temperatura</span>{{ info.currentTemperature }}°C
          </div>
          <div class="col-md-6 text-center ps-0">
            <span class="fw-semibold fs-5 label d-block text-start">Sensação térmica</span>
            <span class="fw-semibold fs-2 text-center pe-5">{{ info.currentRealFeel }}°C</span>
          </div>
        </div>
      </div>
      <div v-else>
        <Preloader />
      </div>
      <div v-show="info.isReadyToShowUp" class="col-md-7 mt-5 pt-4 ms-5">
        <div id="graph">
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
.container {
  background-color: #fff;
  height: 80vh;
  box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;
}

.label {
  color: #757575;
}
</style>
