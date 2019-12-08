<template>
  <div>
    <div ref="webcam"></div>
    <slot name="predictions" :predictions="predictions"></slot>
  </div>
</template>

<script>
import '@tensorflow/tfjs'
import * as tmImage from "@teachablemachine/image"

export default {
  props: {
    url: {
      type: String,
      required: true
    }
  },

  data() {
    return {
      model: null,
      webcam: null,
      predictions: []
    }
  },

  async mounted() {
    const modelURL = `${this.url}model.json`
    const metadataURL = `${this.url}metadata.json`
    const webcamContainer = this.$refs.webcam
    const flip = true // whether to flip the webcam

    // load the model and metadata
    // Refer to tmImage.loadFromFiles() in the API to support files from a file picker
    // or files from your local hard drive
    // Note: the pose library adds "tmImage" object to your window (window.tmImage)
    this.model = await tmImage.load(modelURL, metadataURL)

    // Convenience function to setup a webcam
    this.webcam = new tmImage.Webcam(
      webcamContainer.width,
      webcamContainer.height,
      flip
    ) // width, height, flip
    await this.webcam.setup() // request access to the webcam
    await this.webcam.play()
    webcamContainer.appendChild(this.webcam.canvas)
    
    window.requestAnimationFrame(this.loop)
  },

  methods: {
    async loop() {
      this.webcam.update()
      await this.predict()
      window.requestAnimationFrame(this.loop)
    },

    async predict() {
      this.predictions = await this.model.predict(this.webcam.canvas)
    }
  }
}
</script>