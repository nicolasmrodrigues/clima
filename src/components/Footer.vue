<script setup>
const prop = defineProps([])
</script>

<template>
  <footer class="footer">
    <a class="footer-link d-block mt-4" target="_blank" hreflang="en" href="https://github.com/nicolasmrodrigues"
      title="Nícolas Medeiros's github profile">Created by: Nícolas Medeiros
    </a>
    <a class="footer-link d-block mt-1" target="_blank" hreflang="en" href="https://www.flaticon.com/free-icons/weather"
      title="weather icons">Weather
      icons created by iconixar -
      Flaticon
    </a>

  </footer>
</template>

<style scoped lang="scss">
.footer {
  text-align: center;
  height: 10dvh;

  &-link {
    display: inline-block;
    text-decoration: none;
  }
}
</style>