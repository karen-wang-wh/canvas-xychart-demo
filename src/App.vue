<template>
<!--  <img alt="Vue logo" src="./assets/logo.png" />-->
<!--  <HelloWorld msg="Hello Vue 3 + TypeScript + Vite" />-->
  <div v-for="item in arr">
    <chart class="chart"/>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
// import HelloWorld from './components/HelloWorld.vue'
// import CanvasChart from './components/CanvasChart.vue'
// import LargeDataChart from './components/LargeDataChart.vue'
import EChart from './components/EChart.vue'

// import SvgChart from './components/SvgChart.vue'

export default defineComponent({
  name: 'App',
  components: {
    // HelloWorld
    // chart: CanvasChart
    // chart: LargeDataChart
    chart: EChart
    // chart: SvgChart
  },
  data() {
    return {
      arr: new Array(8)
      // arr: new Array(18)
    }
  },
})
</script>

<style>
body {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
