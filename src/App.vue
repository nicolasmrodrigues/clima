<script setup>
import { reactive } from 'vue';
import Formulario from './components/Formulario.vue';

const apiKey = 'bfb6cd82b7961aa3ba0fcd96665b5e68';
const baseUrl = 'https://api.openweathermap.org/data/2.5/weather';
const baseIconUrl = 'https://openweathermap.org/img/wn/'

const info = reactive({
  city: '',
  tempCity: '',
  requestDate: '',
  iconUrl: '',
  weatherDescription: '',
  humidity: 0,
  wind: 0,
  isReady: false
})

async function getCityData() {
  const url = `${baseUrl}?q=${info.city}&units=metric&lang=pt_br&appid=${apiKey}`;
  const response = await fetch(url);
  const jsonData = await response.json();
  info.iconUrl = `${baseIconUrl}${jsonData.weather[0].icon}@4x.png`;
  const date = new Date();
  console.log(jsonData)

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

  const WeekDays = ['Dom', 'Seg', 'Terç', 'Qua', 'Qui', 'Sex', 'Sáb']
  const months = ['Jan', 'Fev', 'Mar', 'Abr', 'Maio', 'Jun', 'Jul', 'Ago', 'Set', 'Out', 'Nov', 'Dez']

  info.requestDate += `, ${WeekDays[date.getDay()]}`
  info.requestDate += `, ${months[date.getMonth()]}, ${date.getDate()}, ${date.getFullYear()}`

  info.weatherDescription = jsonData.weather[0].description
  info.humidity = jsonData.main.humidity
  info.wind = jsonData.wind.speed
  info.isReady = true

}

</script>

<template>
  <div class="container mt-5 ps-4 rounded-3">
    <Formulario :updates-temp="event => info.tempCity = event.target.value"
      :updates-city="event => info.city = info.tempCity" :get-data="getCityData" />
    <div v-show="info.isReady" class="weather-card">
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
      <div class="row justify-content-center ps-4">
        <div class="col-md-2 text-center pe-0">
          <span class="fw-semibold fs-5 label">Humidade</span>
        </div>
        <div class="col-md-2 text-start ps-0">
          <span class="fw-semibold fs-5 label">Velocidade do vento</span>
        </div>
      </div>
      <div class="row justify-content-center ps-4">
        <div class="col-md-2 text-center pe-0">
          <span class="fw-semibold fs-2">{{ info.humidity }}%</span>
        </div>
        <div class="col-md-2 text-center pe-5">
          <span class="fw-semibold fs-2">{{ info.wind }} m/s</span>
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

.form-control {
  background-color: transparent;
}

.label {
  color: #757575;
}
</style>
