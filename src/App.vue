<template>
  <h1>Hacking with Web RTC</h1>
  <p>Connected devices:</p>
  <ul>
    <li v-for="device in devices">{{ device.kind }}: {{ device.label }}</li>
  </ul>
  <button @click="handleClick">
    {{ !playing ? "Start" : "Stop" }} streaming
  </button>
  <video v-show="playing" ref="videoEl" autoplay playsinline></video>
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
        const constraints = { video: true, audio: true };
        const stream = await navigator.mediaDevices.getUserMedia(constraints);
        this.playing = true;
        this.$refs.videoEl.srcObject = stream;
      } catch (error) {
        console.error("Error opening video camera.", error);
      }
    },
    stopStreaming() {
      const stream = this.$refs.videoEl.srcObject;
      const tracks = stream.getTracks();

      tracks.forEach(function (track) {
        track.stop();
      });
      this.$refs.videoEl.srcObject = null;
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
}
</style>
