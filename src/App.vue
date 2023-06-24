<script setup>
import { onMounted, reactive, ref } from 'vue';
import Formulario from './components/Formulario.vue'

const apiKey = 'c5730d43197b4199a5d174227232306';
const curretWeatherBaseUrl = 'https://api.weatherapi.com/v1/current.json?';
const forecastBaseUrl = 'https://api.weatherapi.com/v1/forecast.json?';
const iconBaseUrl = 'https://cdn.weatherapi.com/weather/128x128/';

const WeekDays = ['Dom', 'Seg', 'Terç', 'Qua', 'Qui', 'Sex', 'Sáb']
const months = ['Jan', 'Fev', 'Mar', 'Abr', 'Maio', 'Jun', 'Jul', 'Ago', 'Set', 'Out', 'Nov', 'Dez']

const info = reactive({
  city: '',
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
  iconUrl: '',
  isReadyToShowUp: false,
  requestDate: '',
  weatherDescription: '',
  wind: 0
})

function getCityData() {
  const curretWeatherUrl = `${curretWeatherBaseUrl}q=${info.city}&lang=pt&key=${apiKey}`;
  fetch(curretWeatherUrl).then(response => {
    response.json().then(jsonData => {
      info.iconUrl = `${iconBaseUrl}${jsonData.current.condition.icon.slice(35, jsonData.current.condition.icon.length)}`
      const date = new Date();

      if (date.getHours() >= 13) {
        info.requestDate = `${date.getHours() % 12}`
        if (date.getMinutes() < 10) {
          info.requestDate += `:0${date.getMinutes()} PM`
        } else {
          info.requestDate += `:${date.getMinutes()} PM`
        }
      } else {
        info.requestDate = `${date.getHours()}`
        if (date.getMinutes() < 10) {
          info.requestDate += `:0${date.getMinutes()} AM`
        } else {
          info.requestDate += `:${date.getMinutes()} AM`
        }
      }

      info.requestDate += `, ${WeekDays[date.getDay()]}`
      info.requestDate += `, ${months[date.getMonth()]}, ${date.getDate()}, ${date.getFullYear()}`

      info.weatherDescription = jsonData.current.condition.text
      info.currentHumidity = jsonData.current.humidity
      info.wind = jsonData.current.wind_kph

      const forecastUrl = `${forecastBaseUrl}q=${info.city}&lang=pt&days=3&key=${apiKey}`;
      fetch(forecastUrl).then(response => {
        response.json().then(jsonData => {
          let forecastjsonData = jsonData.forecast.forecastday
          let forecast = [...forecastjsonData];

          for (let i = 0; i < forecast.length; i++) {
            info.forecastMaxAndMinTemperatures[i].max = Math.round(forecast[i].day.maxtemp_c)
            info.forecastMaxAndMinTemperatures[i].min = Math.round(forecast[i].day.mintemp_c)
          }

          drawGraph()
          info.isReadyToShowUp = true
        })
      })
    })
  })
}

function drawGraph() {
  const date = new Date()
  let addToIndex = 0;
  const graph = document.getElementById('graph')
  const dayToday = WeekDays[date.getDay()]
  let indexOfDayToday = WeekDays.indexOf(dayToday)

  let xValues = []
  let yMinValues = []
  let yMaxValues = []

  for (let i = 0; i < info.forecastMaxAndMinTemperatures.length; i++) {
    yMaxValues.push(info.forecastMaxAndMinTemperatures[i].max)
  }

  for (let i = 0; i < 3; i++) {
    addToIndex += 1
    if (indexOfDayToday + addToIndex === 7) {
      indexOfDayToday = 0;
      addToIndex = 0;
    }
    xValues.push(`${WeekDays[indexOfDayToday + addToIndex]}.`)
  }

  let trace1 = {
    x: xValues,
    y: yMaxValues,
    type: 'bar',
    name: 'Máxima',
    text: yMaxValues.map(String),
    textposition: 'auto',
    hoverinfo: 'none',
    marker: {
      color: 'rgb(58,200,225)',
      line: {
        color: 'rgb(8,48,107)',
        width: 1.5
      }
    }
  };

  for (let i = 0; i < info.forecastMaxAndMinTemperatures.length; i++) {
    yMinValues.push(info.forecastMaxAndMinTemperatures[i].min)
  }

  let trace2 = {
    x: xValues,
    y: yMinValues,
    type: 'bar',
    name: 'Mínima',
    text: yMinValues.map(String),
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
  };

  let data = [trace1, trace2];

  let layout = {
    title: 'Temperaturas mínimas e máximas',
    font: { size: 18 },
    barmode: 'group',
    width: 700,
    height: 410,
  };

  let config = { staticPlot: true, responsive: true }
  Plotly.newPlot(graph, data, layout, config);
}

</script>

<template>
  <div class="container mt-5 ps-4 rounded-3">
    <Formulario :updates-city="event => info.city = event.target.value" :get-data="getCityData" />
    <div class="forecast-container d-flex">
      <div class="col-md-4 weather-card d-inline-block" v-if="info.isReadyToShowUp">
        <div class="row mt-5">
          <div class="col-md-12 text-center">
            <span v-if="info.requestDate">{{ info.requestDate }}</span>
          </div>
        </div>
        <div class="row">
          <div class="col-md-12 text-center">
            <img :src="info.iconUrl" alt="">
          </div>
        </div>
        <div class="row ps-4">
          <div class="col-md-6 text-center fw-semibold fs-2 pe-0">
            <span class="fw-semibold fs-5 label d-block">Humidade</span>{{ info.currentHumidity }}%
          </div>
          <div class="col-md-6 text-center ps-0">
            <span class="fw-semibold fs-5 label d-block text-start">Velocidade do vento</span>
            <span class="fw-semibold fs-2 text-center pe-4">{{ info.wind }} km/h</span>
          </div>
        </div>
      </div>
      <div v-show="info.isReadyToShowUp" class="col-md-6">
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
