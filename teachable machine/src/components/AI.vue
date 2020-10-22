<template>
  <div id="app">
    <h1>합니다</h1>
    <div>Teachable Machine Pose Model</div>
    <button type="button" @click="start()">Start</button>
    <h1 id="now"></h1>
    <div><canvas id="canvas"></canvas></div>
    <div id="label-container"></div>
  </div>
</template>

<script>
import "@tensorflow/tfjs"
import * as tmPose from "@teachablemachine/pose"
let model, webcam, ctx, labelContainer, maxPredictions
export default {
  name: 'AI',
  components: {
  },
  data() {
    return {
      Id: null
    }
  },
  methods: {
    start() {
      this.tmstart()
    },
    async tmstart() {
      const nowH1 = document.getElementById("now")
      nowH1.innerText = '클릭은 된 거 같아요'
      const URL = '../mymodel/'
      console.log(URL)
      const modelURL = URL + "model.json"
      const metadataURL = URL + "metadata.json"
      model = await tmPose.load(modelURL, metadataURL)
      maxPredictions = model.getTotalClasses()
      const size = 400
      const flip = true
      webcam = new tmPose.Webcam(size, size, flip)
      await webcam.setup()
      await webcam.play()
      window.requestAnimationFrame(this.loop)

      const canvas = document.getElementById("canvas")
      canvas.width = size
      canvas.height = size
      ctx = canvas.getContext("2d")
      labelContainer = document.getElementById("label-container")
      for (let i = 0; i < maxPredictions; i++) {
        labelContainer.appendChild(document.createElement("div"))
      }
    },
    async loop() {
      webcam.update()
      await this.predict()
      if (this.Id) {this.Id = window.requestAnimationFrame(this.loop)}
      window.requestAnimationFrame(this.loop)
    },
    async predict() {
      const { pose, posenetOutput } = await model.estimatePose(webcam.canvas)
      const prediction = await model.predict(posenetOutput)

      for (let i = 0; i < maxPredictions; i++) {
        const classPrediction = prediction[i].className + ": " + prediction[i].probability.toFixed(2)
        labelContainer.childNodes[i].innerHTML = classPrediction
      }
      this.drawPose(pose)
    },
    drawPose(pose) {
      if (webcam.canvas) {
        ctx.drawImage(webcam.canvas, 0, 0)
        if (pose) {
          const minPartConfidence = 0.5;
          tmPose.drawKeypoints(pose.keypoints, minPartConfidence, ctx);
          tmPose.drawSkeleton(pose.keypoints, minPartConfidence, ctx);
        }
      }
    }
  }
}
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
</style>
