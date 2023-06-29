<script setup>
const props = defineProps(['submit', 'inputChange', 'buttonClick', 'city1', 'city2', 'city3'])
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
      <div v-show="city1" class="row autocomplete-row ms-3 mt-2" :class="{ last: !city2 }">
        <button class="col-md-12 autocomplete-col pe-1 text-start" id="city1" :value="city1">
          {{ city1 }}
        </button>
      </div>
      <div v-show="city2" class="row autocomplete-row ms-3" :class="{ last: !city3 }">
        <button class="col-md-12 autocomplete-col pe-1 text-start" id="city2" :value="city2">
          {{ city2 }}
        </button>
      </div>
      <div v-show="city3" class="row autocomplete-row ms-3" :class="{ last: city3 }">
        <button class="col-md-12 autocomplete-col pe-1 text-start" id="city3" :value="city3">
          {{ city3 }}
        </button>
      </div>
    </div>

  </form>
</template>

<style scoped lang="scss">
.last {
  button {
    border-bottom-left-radius: 4px;
    border-bottom-right-radius: 4px;
  }
}

.autocomplete {
  height: 120px;
  position: absolute;

  &,
  &-row {
    width: 217px;
    height: 40px;
  }

  &-row:first-child {
    button {
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

    &:hover {
      background-color: rgb(235, 235, 235);
    }
  }
}
</style>