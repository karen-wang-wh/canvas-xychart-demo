<template>
  <div class="container">
    <canvas
        width="1000"
        height="1000"
        ref="canvas"
    ></canvas>
    <canvas
        width="1000"
        height="1000"
        ref="axis"
    ></canvas>
  </div>
</template>

<script>
import { defineComponent } from 'vue'
export default defineComponent({
  name: "CanvasChart",
  data() {
    return {
      canvas: null,
      height: 0,
      width: 0
    }
  },
  methods: {
    init() {},
  },
  mounted() {
    class Point {
      constructor(x, y) {
        this.x = x;
        this.y = y;
      }
      toString() {
        return `Point{x=${this.x}, y=${this.y}}`;
      }
    }
    class Axis {
      max = 0;
      min = 0;
      label = [];
    }
    let p = new Point(0, 1);

    this.$refs.canvas.style.zIndex = 9;
    this.$refs.axis.style.zIndex = 1;
    let canvas = this.$refs.canvas.getContext("2d")

    canvas.strokeStyle = 'black'
    canvas.lineWidth = 2
    this.width = canvas.canvas.width
    this.height = canvas.canvas.height
    let axis = this.$refs.axis.getContext("2d")
    let obj = {
      x: {
        max: 900,
        min: -100,
        aXis: 0,
        scale: undefined,
      },
      y: {
        max: 900,
        min: -100,
        aXis: 0,
        scale: undefined,
      },
      axis: axis,
      canvas: canvas,
      index: 0,
      lastData: [],
      randomData() {
        return Math.floor(Math.random() * (this.y.max - this.y.min)) + this.y.min
      },
      drawAxisX() {
        this.axis.beginPath();
        this.axis.strokeStyle = 'red'
        this.axis.lineWidth = 10 * this.axis.canvas.clientHeight / this.axis.canvas.height;
        this.axis.moveTo(this.transformX(this.x.aXis), 0);
        this.axis.lineTo(this.transformX(this.x.aXis), this.axis.canvas.height);
        this.axis.stroke();
      },
      drawAxisY() {
        this.axis.beginPath();
        this.axis.strokeStyle = 'red';
        this.axis.lineWidth = 10 * this.axis.canvas.clientWidth / this.axis.canvas.width;
        this.axis.moveTo(0, this.transformY(this.y.aXis));
        this.axis.lineTo(this.axis.canvas.width, this.transformY(this.y.aXis));
        this.axis.stroke();
      },
      drawAxis() {
        this.drawAxisX();
        this.drawAxisY();
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
      push(y) {
        let cycleFlag = false
        if (this.index > this.x.max) {
          this.index = this.x.min
          cycleFlag = true
        }
        let data = this.transform([this.index, y])
        if (cycleFlag) {
          this.canvas.clearRect(0, 0, 20, 1000)
          this.canvas.beginPath()
          this.canvas.moveTo(data[0], data[1])
        } else {
          this.canvas.clearRect(data[0], 0, 20, 1000)
          this.canvas.beginPath()
          this.canvas.moveTo(this.lastData[0], this.lastData[1])
          this.canvas.lineTo(data[0], data[1])
        }
        this.canvas.stroke()
        this.lastData = data
        this.index++
      },
    }
    let arr = [];
    setInterval(() => {
      let now = new Date().getTime();
      for (let i = 0; i < 50; i++) {
        arr.push([now, obj.randomData()]);
        now += 10;
      }
      // console.log(arr)
    }, 500);

    function step() {
      let now = new Date().getTime();
      if (arr.length > 0) {
        let a = arr.filter(item => item[0] < now)
        for (let i = 0; i < a.length; i++) {
          let item = arr.shift();
          obj.push(item[1]);
        }
      }
      window.requestAnimationFrame(step)
    }
    obj.drawAxis()
    obj.index = obj.x.min
    window.requestAnimationFrame(step)
  }
})
</script>

<style scoped>

.container {
  left: 0;
  position: relative;
  height: 60px;
  width: 510px;
}
canvas {
  position: absolute;
  border: 2px solid red;
  height: 50px;
  width: 500px;
}
</style>