<script setup>
const props = defineProps(['info'])
</script>

<template>
  <div class="col-xl-4 col-md-5 col-sm-12 weather-card d-inline-block">
    <div class="container">
      <div class="row">
        <div class="col-md-12 text-center local-time">
          <span class="d-block" v-if="info.localDateFormatted">Horário de {{ info.requestedCity }}: </span>
          <span v-if="info.localDateFormatted">{{ info.localDateFormatted }}</span>
        </div>
      </div>
      <div class="row">
        <div class="col-md-12 text-center">
          <img class="weather-icon" :src="info.iconUrl" alt="">
        </div>
      </div>
    </div>
    <div class="info-container">
      <div class="row text-nowrap">
        <div class="col-md-6 text-center px-0">
          <span class="fw-semibold label d-block">Humidade</span>
          <span class="fw-semibold info h2">{{ info.currentHumidity }}%</span>
        </div>
        <div class="col-md-6 text-center px-0 windspeed-col">
          <span class="fw-semibold label d-block">Velocidade do vento</span>
          <span class="fw-semibold info h2 info-windspeed">{{ info.windSpeed }} km/h</span>
        </div>
      </div>
      <div class="row text-nowrap">
        <div class="col-md-6 text-center px-0">
          <span class="fw-semibold label d-block">Temperatura</span>
          <span class="fw-semibold info h2 text-center">{{ info.currentTemperature }}°C</span>
        </div>
        <div class="col-md-6 text-center px-0 realfeel-col">
          <span class="fw-semibold label d-block">Sensação térmica</span>
          <span class="fw-semibold info h2 info-realfeel">{{ info.currentRealFeel }}°C</span>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
.info-container {
  @media (orientation: portrait) and (max-width: 767px) {
    display: flex;
  }
}

.row:last-child {
  margin-top: 24px;

  @media (max-height: 600px) {
    margin-top: 0;
  }

  @media (orientation: portrait) and (min-width: 768px) {
    margin-top: 16px;
  }

  @media (orientation: portrait) and (max-width: 767px) {
    margin-top: 0;
  }
}

.realfeel-col {

  @media (orientation: portrait) and (max-width: 767px) {
    margin-top: 16px;
  }
}

.windspeed-col {
  @media (orientation: portrait) and (max-width: 767px) {
    margin-top: 16px;
  }

}

.local-time {
  @media (orientation: portrait) and (min-width: 992px) {
    font-size: 24px;
  }

  @media (orientation: portrait) and (max-width: 767px) {
    font-size: 18px;
  }
}

.label {
  font-size: 1.25rem;
  color: #757575;

  @media (max-height: 600px) or (max-width: 1400px) {
    font-size: 1.15rem;
  }

  @media (max-height: 500px) or (max-width: 991px) {
    font-size: 1rem;
  }

  @media (orientation: portrait) and (min-width: 768px) {
    font-size: 1.75rem;
  }
}

.weather-icon {

  @media (max-height: 550px) or (max-width: 991px) {
    width: 80px;
  }

  @media (orientation: portrait) or (max-width: 767px) {
    width: 100px;
  }
}

.info {

  @media (orientation: landscape) and (max-height: 500px) {
    font-size: 1.25rem;
  }
}

.weather-card {
  margin-top: 48px;

  @media (orientation: portrait) {
    flex: 0 0 auto;
    width: 100%;
    margin-top: 24px;
  }

  @media (max-height: 650px) {
    margin-top: 16px;
  }

  @media (max-height: 600px) {
    max-height: 50dvh;
  }
}
</style>