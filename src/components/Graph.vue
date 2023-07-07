<script setup>
</script>

<template>
  <div class="col-xl-7 col-md-5 col-6" id="canvas-container">
    <canvas id="canvas">
    </canvas>
  </div>
</template>

<style lang="scss">
#canvas-container {
  max-height: 55dvh;
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

  @media (orientation: portrait) {
    width: 85%;
    margin: 3dvh auto 0 auto;
    max-height: 33dvh;
    height: 100%;
  }

  @media (max-width: 767px) {
    display: none;
  }
}
</style>