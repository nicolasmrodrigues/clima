<script setup>
const props = defineProps([])
</script>

<template>
  <div class="col-xl-7 col-md-5 col-6" id="canvas-container">
    <canvas id="canvas">
    </canvas>
  </div>
</template>

<style lang="scss">
#canvas-container {
  max-height: 53dvh;
  margin-top: 24px;
  margin: 24px 0px 0px 48px;

  @media (max-height: 700px) {
    max-height: 57dvh;
  }

  @media (max-height: 650px) {
    margin-top: 4px;
  }

  @media (max-height: 600px) {
    max-height: 55dvh;
  }

  @media (max-width: 991px) {
    max-height: 45dvh;
  }

  @media (orientation: portrait) {
    width: 85%;
    margin: 2dvh auto 0 auto;
    max-height: 50dvh;
  }

  @media (max-width: 767px) {
    display: none;
  }

  @media (orientation: portrait) and (min-height: 992px) {
    width: 40%;
  }
}
</style>