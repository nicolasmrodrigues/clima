<script setup>
</script>

<template>
  <footer class="footer">
    <a class="footer-link d-block" target="_blank" hreflang="en" href="https://github.com/nicolasmrodrigues"
      title="Nícolas Medeiros's github profile">Created by: Nícolas Medeiros
    </a>
    <a class="footer-link d-block" target="_blank" hreflang="en" href="https://www.flaticon.com/free-icons/weather"
      title="weather icons">Weather
      icons created by iconixar -
      Flaticon
    </a>

  </footer>
</template>

<style scoped lang="scss">
.footer {
  text-align: center;

  @media (max-height: 400px) {
    font-size: 14px;
  }

  &-link {
    display: inline-block;
    text-decoration: none;

    &:first-child {
      padding-top: 1.5dvh;

      @media (max-height: 400px) {
        padding: 0;
      }
    }
  }
}
</style>