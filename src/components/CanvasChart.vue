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
      },
      y: {
        max: 900,
        min: -100,
        aXis: 0,
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
          (arr[0] - this.x.min) * this.scaleX(),
          (this.y.max - arr[1]) * this.scaleY(),
        ]
      },
      scaleX() {
        return this.canvas.canvas.width / (this.x.max - this.x.min)
      },
      scaleY() {
        return this.canvas.canvas.height / (this.y.max - this.y.min);
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
    function step() {
      for (let i = 0; i < 2; i++) {
        obj.push(obj.randomData())
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
  height: 120px;
  width: 1020px;
}
canvas {
  position: absolute;
  border: 2px solid red;
  height: 100px;
  width: 1000px;
}
</style>