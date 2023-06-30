<script setup>
const props = defineProps(['submit', 'inputChange', 'buttonClick', 'cities'])
</script>

<template>
  <form @submit.prevent="submit">
    <div class="row px-3">
      <div class="col-md-2">
        <input @keyup="inputChange" type="text" id="city-input" spellcheck="false" autocomplete="off"
          class="bg-trasparent form-control">
      </div>
      <div class="col-md-1">
        <button @click="buttonClick" type="submit" class="btn btn-primary"><i class="bi bi-search"></i></button>
      </div>
    </div>
    <div class="autocomplete">
      <div v-show="cities[0]" class="row autocomplete-row ms-3 mt-2" :class="{ last: !cities[1] }">
        <button class="col-md-12 autocomplete-col pe-1 text-start" id="city1" :value="cities[0]">
          {{ cities[0] }}
        </button>
      </div>
      <div v-show="cities[1]" class="row autocomplete-row ms-3" :class="{ last: !cities[2] }">
        <button class="col-md-12 autocomplete-col pe-1 text-start" id="city2" :value="cities[1]">
          {{ cities[1] }}
        </button>
      </div>
      <div v-show="cities[2]" class="row autocomplete-row ms-3" :class="{ last: cities[2] }">
        <button class="col-md-12 autocomplete-col pe-1 text-start" id="city3" :value="cities[2]">
          {{ cities[2] }}
        </button>
      </div>
    </div>
  </form>
</template>

<style scoped lang="scss">
.last {
  .autocomplete-col {
    border-bottom-left-radius: 4px;
    border-bottom-right-radius: 4px;
    border: none;
  }
}

.selected {
  background-color: rgb(235, 235, 235) !important;
}

.autocomplete {
  height: 120px;
  position: absolute;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 14px;

  &,
  &-row {
    width: 217px;
    height: 40px;
  }

  &-row:first-child {
    .autocomplete-col {
      border-top-left-radius: 4px;
      border-top-right-radius: 4px;
    }
  }

  &-col {
    box-shadow: rgba(17, 17, 26, 0.05) 2px 2px 0px, rgba(17, 17, 26, 0.1) 0px 2px 2px;
    border: none;
    background-color: rgb(245, 245, 245);
    max-height: 40px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    border-bottom: 2px solid #e3e3e3;

    &:hover {
      background-color: rgb(235, 235, 235);
    }
  }
}
</style>