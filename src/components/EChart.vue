<template>
  <div
      class="container"
      :style="{height: `${height}px`, width: `${width}px`}"
      ref="chart"
  >
  </div>
</template>

<script>
import {defineComponent} from 'vue';
import * as echarts from 'echarts';
import { hz, ReSampler } from '../data/xyData';

const arr = [];
// 8h
const duration = 3600 * 1000 * 8;

// 1h
// const duration = 3600 * 1000;

// 30 min
// const duration = 60 * 1000 * 5;

// 1 min
// const duration = 60 * 1000;

// 30 s
// const duration = 30 * 1000;

// 5 s
// const duration = 5 * 1000;
const length = duration / 10;
const start = new Date("2021/01/01 12:00:00").getTime()
let time = start
// 1hz origin data
let origin = [
  81, 76, 21, 49, 23, -28, 26, -12, -40, 48,
  -2, -1, -12, 6, -23, 10, -13, -48, -104, -4,
  33, 174, 961, -1568, -603, 265, 413, 346, 237, 154,
  91, 44, -21, -29, 34, 14, 22, 20, -31, -153,
  -203, -266, -286, -182, -107, -7, 63, 98, 134, 108,
  80, 93, 84, 65, 47, 37, 35, 32, 11, 19,
  -2, -3, -6, -19, 57, 42, -25, -58, -42, -81,
  -37, 25, 363, 532, -1504, -304, 410, 408, 320, 222,
  118, 56, 55, 4, -39, 16, 26, 16, 12, -63,
  -146, -257, -287, -268, -152, -47, 32, 23, 143, 94
]

origin = hz(origin, 10)

// console.log(point.length)
// console.log('origin', JSON.stringify(origin))
let min = -2000
// let min = -1568
// let max = 961
let max = 1500
console.time("generate")
for (let i = 0; i < length; i++) {
  arr.push({time: time, value: origin[i % origin.length]})
  time += 10;
}
console.timeEnd("generate")

export default defineComponent({
  name: "EChart",
  data() {
    return {
      canvas: null,
      height: 0,
      width: 0,
    }
  },
  methods: {
    init() {
    },
  },
  beforeMount() {
    this.height = window.innerHeight / 8
    this.width = window.innerWidth
  },
  mounted() {
    let chart = echarts.init(this.$refs.chart)

    let reSampler = new ReSampler(Math.floor(chart.getWidth() * 0.8));
    let result = reSampler.resample(arr.map(item => {
      return {x:item.time, y:item.value}
    }))
    console.log(result)
    chart.setOption({
      xAxis: {
        type: 'time',
      },
      grid: {
        width: Math.floor(chart.getWidth() * 0.8),
        top: '1',
        bottom: '1',
        // left: '1',
        // right: '1',
      },
      yAxis: {
        type: 'value'
      },
      series: [{
        symbol: 'none',
        data: result.map(item => {return [item.x, item.y]}),
        // data: arr.map(item => {return [item.time, item.value]}),
        // sampling: 'lttb',
        type: 'line',
      }]
    })
  }
})
</script>

<style scoped>

.container {
  left: 0;
  /*position: relative;*/
  /*height: 100%;*/
  /*width: 100%;*/
}

canvas {
  /*position: absolute;*/
  border: 2px solid red;
  height: 100%;
  width: 100%;
  /*height: 50px;*/
  /*width: 500px;*/
}
</style>