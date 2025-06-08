<script setup lang="ts">
import {ref} from "vue";

const divArray = ref(['', '', '', '', '', '', '', '', ''])

function changeDivColour(i: number) {
  if (divArray.value[i] === '') {
    divArray.value[i] = 'background-color: #D86E6EFF;'
  } else {
    divArray.value[i] = '';
  }
}

</script>

<template>
  <div>
    <div class="container">
      <div v-for="i in [0, 1, 2]" @click="changeDivColour(i)" :style="divArray[i]">{{ i + 1 }}</div>
    </div>
    <div class="container">
      <div v-for="i in [3, 4, 5]" @click="changeDivColour(i)" :style="divArray[i]">{{ i + 1 }}</div>
    </div>
    <div class="container">
      <div v-for="i in [6, 7, 8]" @click="changeDivColour(i)" :style="divArray[i]">{{ i + 1 }}</div>
    </div>
  </div>
</template>

<style scoped>

.container {
  display: flex;
  justify-content: center;
}

.container div {
  padding: 50px;
  border: #000000 1px solid;
}
</style>
