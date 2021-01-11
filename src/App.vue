<template>
  <h1>Hacking with Web RTC</h1>
  <p>Connected devices:</p>
  <ul>
    <li v-for="device in devices">{{ device.kind }}: {{ device.label }}</li>
  </ul>
  <button @click="handleClick">
    {{ !playing ? "Start" : "Stop" }} streaming
  </button>
  <video v-show="playing" ref="cameraVideoEl" autoplay playsinline></video>
  <video v-show="playing" ref="screenVideoEl" autoplay playsinline></video>
</template>

<script>
export default {
  name: "WebRTC",
  data() {
    return {
      devices: [],
      playing: false,
    };
  },
  methods: {
    async getConnectedDevices() {
      this.devices = await navigator.mediaDevices.enumerateDevices();
    },
    handleClick() {
      !this.playing ? this.startStreaming() : this.stopStreaming();
    },
    async startStreaming() {
      try {
        let constraints = { video: true, audio: true };
        let stream = await navigator.mediaDevices.getUserMedia(constraints);
        this.playing = true;
        this.$refs.cameraVideoEl.srcObject = stream;
      } catch (error) {
        console.error("Error opening video camera.", error);
      }
      try {
        let constraints = {
          video: {
            cursor: "motion",
            displaySurface: "monitor",
          },
        };
        let screenStream = await navigator.mediaDevices.getDisplayMedia(
          constraints
        );
        this.playing = true;
        this.$refs.screenVideoEl.srcObject = screenStream;
        console.log(screenStream.getTracks());
      } catch (error) {
        console.error("Error opening screen capture.", error);
      }
    },
    stopStreaming() {
      let stream = this.$refs.cameraVideoEl.srcObject;
      let tracks = stream.getTracks();

      tracks.forEach(function (track) {
        track.stop();
      });
      this.$refs.cameraVideoEl.srcObject = null;
      this.playing = false;

      stream = this.$refs.screenVideoEl.srcObject;
      tracks = stream.getTracks();

      tracks.forEach(function (track) {
        track.stop();
      });
      this.$refs.screenVideoEl.srcObject = null;
      this.playing = false;
    },
  },
  mounted() {
    this.getConnectedDevices();
  },
};
</script>

<style>
video {
  border-radius: 6px;
  width: 100%;
}
</style>
