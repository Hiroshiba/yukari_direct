<template>
  <div class="hello">
    hello hiho!
    <button @click="startRecord">start!</button>
    <button @click="stopRecord">stop!</button>
    <audio controls id="audio"></audio>
  </div>
</template>

<script>
export default {
  data () {
    return {
      stream: null,
      mediaRecorder: null
    }
  },

  mounted () {
    navigator.mediaDevices.getUserMedia({audio: true, video: false})
      .then((stream) => {
        this.stream = stream
        this.mediaRecorder = new MediaRecorder(stream)
        this.mediaRecorder.ondataavailable = this.onFinishedReocrd
      })
  },

  methods: {
    startRecord () {
      this.mediaRecorder.start()
    },

    stopRecord () {
      this.mediaRecorder.stop()
    },

    onFinishedReocrd (event) {
      const formData = new FormData()
      formData.append('data', event.data)

      const url = 'http://kurisu39.kurisu.nico:9999/yukari'

      fetch(url, {
        body: formData,
        method: 'POST',
        mode: 'cors'
      })
        .then(response => response.blob())
        .then(blob => {
          const audio = document.getElementById('audio')
          audio.src = window.URL.createObjectURL(blob)
        })
    }
  }
}
</script>
