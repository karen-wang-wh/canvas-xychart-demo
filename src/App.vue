<template>
<!--  <img alt="Vue logo" src="./assets/logo.png" />-->
<!--  <HelloWorld msg="Hello Vue 3 + TypeScript + Vite" />-->
  <div v-for="item in arr">
    <canvas-chart class="chart"></canvas-chart>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
// import HelloWorld from './components/HelloWorld.vue'
import CanvasChart from './components/CanvasChart.vue'

export default defineComponent({
  name: 'App',
  components: {
    // HelloWorld
    CanvasChart
  },
  data() {
    return {
      arr: new Array(18)
      // arr: new Array(18)
    }
  },
})
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
/*.chart {*/
/*  height: 100px;*/
/*  width: 1000px;*/
/*}*/
</style>
