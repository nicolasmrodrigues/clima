<script setup>
const props = defineProps(['submit', 'inputChange', 'buttonClick'])
</script>

<template>
  <form @submit.prevent="props.submit">
    <div class="row px-3">
      <div class="col-md-2">
        <input @keyup="props.inputChange" type="text" id="city-input" spellcheck="false"
          class="bg-trasparent form-control">
      </div>
      <div class="col-md-1">
        <button @click="props.buttonClick" type="submit" class="btn btn-primary"><i class="bi bi-search"></i></button>
      </div>
    </div>
  </form>
</template>
