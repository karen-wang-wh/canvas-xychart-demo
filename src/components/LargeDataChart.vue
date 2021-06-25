<template>
  <div class="container" :style="{height: `${height}px`, width: `${width}px`}">
    <canvas
        ref="canvas"
    ></canvas>
  </div>
</template>

<script>
import {defineComponent} from 'vue'
import { hz, ReSampler, Point } from '../data/xyData';

const arr = [];
// 8h
// const duration = 3600 * 1000 * 8;

// 1h
// const duration = 3600 * 1000;

// 30 min
// const duration = 60 * 1000 * 5;

// 1 min
const duration = 60 * 1000;

// 30 s
// const duration = 30 * 1000;

// 5 s
// const duration = 5 * 1000;
const length = duration / 10;
const start = new Date("2021/01/01 12:00:00").getTime()
let time = start
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

origin = hz(origin, 1)

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
// console.log(arr);
// console.log(JSON.stringify(arr).length)
console.timeEnd("generate")

export default defineComponent({
  name: "LargeDataChart",
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
    this.height = window.innerHeight
    this.width = window.innerWidth
  },
  mounted() {
    this.canvas = this.$refs.canvas
    let canvas = this.$refs.canvas.getContext("2d")

    // this.$refs.canvas.onmousemove = (e => {
    //   // console.log(e.offsetX, e.offsetY)
    // })
    let ratio = window.devicePixelRatio
    // let ratio = 1
    canvas.canvas.height = canvas.canvas.clientHeight * ratio
    canvas.canvas.width = canvas.canvas.clientWidth * ratio
    console.log(canvas.canvas.height, canvas.canvas.width)
    console.log(canvas.canvas.clientHeight, canvas.canvas.clientWidth)

    canvas.strokeStyle = 'black'
    canvas.lineWidth = ratio
    let obj = {
      data: null,
      x: {
        max: arr[arr.length - 1].time,
        min: arr[0].time,
        aXis: 0,
        scale: undefined,
      },
      y: {
        max: max,
        min: min,
        aXis: 0,
        scale: undefined,
      },
      // axis: axis,
      canvas: canvas,
      lastData: [0, 0],
      randomData() {
        return Math.floor(Math.random() * (this.y.max - this.y.min)) + this.y.min
      },
      transform(arr) {
        return [
          this.transformX(arr[0]),
          this.transformY(arr[1]),
        ]
      },
      scaleX() {
        if (!this.x.scale) {
          this.x.scale = this.canvas.canvas.width / (this.x.max - this.x.min)
        }
        return this.x.scale
      },
      scaleY() {
        if (!this.y.scale) {
          this.y.scale = this.canvas.canvas.height / (this.y.max - this.y.min);
        }
        return this.y.scale
      },
      transformX(x) {
        return (x - this.x.min) * this.scaleX();
      },
      transformY(y) {
        return (this.y.max - y) * this.scaleY();
      },
      draw0(arr) {
        console.time('draw')
        this.canvas.beginPath()
        arr.forEach((item, index) => {
          let data = this.transform(item)
          if (index === 0) {
            this.canvas.moveTo(data[0], data[1])
          } else {
            this.canvas.lineTo(data[0], data[1])
            if (index % 1000 === 0) {
              this.canvas.stroke()
              this.canvas.beginPath()
            }
          }
        })
        this.canvas.stroke()
        console.timeEnd('draw')
      },
      draw(arr) {
        this.data = arr

        let reSampler = new ReSampler(this.canvas.canvas.width);
        let result = reSampler.resample(arr.map(item => {
          return {x:item.time, y:item.value}
        }))
        console.log(result)
        this.draw0(result.map(item => {
          return [item.x, item.y]
        }))

      }
    }
    obj.draw(arr)
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