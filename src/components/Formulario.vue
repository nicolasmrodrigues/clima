<script setup>
const props = defineProps(['getData', 'updatesTemp', 'updatesCity'])
</script>

<template>
  <form @submit.prevent="getData">
    <div class="row pt-5">
      <div class="col-md-1">
        <label class="col-form-label" for="city-input">Sua cidade:</label>
      </div>
      <div class="col-md-2 ps-0">
        <input @keyup="updatesTemp" type="text" class="form-control" id="city-input">
      </div>
      <div class="col-md-1">
        <button type="submit" @click="updatesCity" class="btn btn-primary">Pesquisar</button>
      </div>
    </div>
  </form>
</template>
