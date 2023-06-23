<script setup>
const props = defineProps(['getData', 'updatesCity'])
</script>

<template>
  <form @submit.prevent="getData">
    <div class="row pt-5">
      <div class="col-md-1">
        <label class="col-form-label" for="city-input">Sua cidade:</label>
      </div>
      <div class="col-md-2 ps-0">
        <input @keyup="updatesCity" type="text" class="bg-trasparent form-control">
      </div>
      <div class="col-md-1">
        <button type="submit" class="btn btn-primary">Pesquisar</button>
      </div>
    </div>
  </form>
</template>

<style scoped lang="scss">
.bg-trasparent {
  background-color: transparent;
}
</style>