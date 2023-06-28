<script setup>
const props = defineProps(['info'])
</script>

<template>
  <div class="col-md-4 weather-card d-inline-block mt-5">
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
</template>

<style scoped>
.label {
  color: #757575;
}
</style>