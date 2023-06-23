<script setup>
import { onMounted, reactive, ref } from 'vue';
import Formulario from './components/Formulario.vue'
import { x } from 'plotly.js-dist';
import { add } from 'plotly.js-dist';

const apiKey = 'bfb6cd82b7961aa3ba0fcd96665b5e68';
const baseUrl = 'https://api.openweathermap.org/data/2.5/weather';
const baseMultiDays = 'https://api.openweathermap.org/data/2.5/forecast';
const baseIconUrl = 'https://openweathermap.org/img/wn/'

const WeekDays = ['Dom', 'Seg', 'Terç', 'Qua', 'Qui', 'Sex', 'Sáb']
const months = ['Jan', 'Fev', 'Mar', 'Abr', 'Maio', 'Jun', 'Jul', 'Ago', 'Set', 'Out', 'Nov', 'Dez']

const info = reactive({
  city: '',
  requestDate: '',
  iconUrl: '',
  weatherDescription: '',
  humidity: 0,
  wind: 0,
  isReadyToShowUp: false,
  daysAverageTemperature: [21, 22, 22, 22]
})

function getCityData() {
  const url = `${baseUrl}?q=${info.city}&units=metric&lang=pt_br&appid=${apiKey}`;
  fetch(url).then(response => {
    response.json().then(response => {
      const jsonData = response;
      info.iconUrl = `${baseIconUrl}${jsonData.weather[0].icon}@4x.png`;
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

      info.weatherDescription = jsonData.weather[0].description
      info.humidity = jsonData.main.humidity
      info.wind = jsonData.wind.speed

      const mutltiDaysUrl = `${baseMultiDays}?lat=${jsonData.coord.lat}&lon=${jsonData.coord.lon}&units=metric&lang=pt_br&appid=${apiKey}`;
      fetch(mutltiDaysUrl).then(response => {
        response.json().then(response => {
          const mutltiDaysJsonData = response;
          let mutltiDaysForecast = [];
          let mutltiDaysForecastAvaregeTemperature = [0, 0, 0, 0]
          mutltiDaysJsonData.list.forEach(forecast => {
            const forecastDay = forecast.dt_txt.slice(8, 10)

            if (forecastDay > date.getDate() && forecastDay != date.getDate() + 5) {
              mutltiDaysForecast.push(forecast)
            }
          })

          for (let i = 1; i <= mutltiDaysForecast.length; i++) {
            const index = Math.ceil(i / 8) - 1;
            mutltiDaysForecastAvaregeTemperature[index] += Math.ceil(mutltiDaysForecast[i - 1].main.temp);
          }

          for (let i = 0; i < mutltiDaysForecastAvaregeTemperature.length; i++) {
            mutltiDaysForecastAvaregeTemperature[i] = Math.ceil(mutltiDaysForecastAvaregeTemperature[i] / 8);
          }
          info.daysAverageTemperature = mutltiDaysForecastAvaregeTemperature
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
  let yValues = info.daysAverageTemperature

  for (let i = 0; i < 4; i++) {
    addToIndex += 1
    if (indexOfDayToday + addToIndex === 7) {
      indexOfDayToday = 0;
      addToIndex = 0;
    }
    xValues.push(`${WeekDays[indexOfDayToday + addToIndex]}.`)
  }

  let trace = {
    x: xValues,
    y: yValues,
    type: 'bar',
    text: yValues.map(String),
    textposition: 'auto',
    hoverinfo: 'none',
    marker: {
      color: 'rgb(158,202,225)',
      opacity: 0.6,
      line: {
        color: 'rgb(8,48,107)',
        width: 1.5
      }
    }
  };
  let data = [trace];
  let layout = {
    title: 'Temperatura média nos proximos dias',
    font: { size: 18 },
    barmode: 'stack',
    width: 700,
    height: 410
  };
  var config = { staticPlot: true, responsive: true }
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
          <div class="col-md-6 text-center pe-0">
            <span class="fw-semibold fs-5 label">Humidade</span>
          </div>
          <div class="col-md-6 text-start ps-0">
            <span class="fw-semibold fs-5 label">Velocidade do vento</span>
          </div>
        </div>
        <div class="row ps-4">
          <div class="col-md-6 text-center pe-0">
            <span class="fw-semibold fs-2">{{ info.humidity }}%</span>
          </div>
          <div class="col-md-6 text-center pe-5">
            <span class="fw-semibold fs-2">{{ info.wind }} m/s</span>
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
