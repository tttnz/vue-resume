<template>
  <svg
    width="100%"
    height="15"
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
  >
    <rect
      x="0"
      y="0"
      width="100%"
      height="15"
      :fill="backColor"
      stroke="none"
      stroke-width="0"
    />

    <rect
      x="0"
      y="0"
      :width="percent"
      height="15"
      :fill="frontColor"
      stroke="none"
      stroke-width="0"
    />
  </svg>
</template>

<script>
export default {
  data() {
    return {
      timer: this.delay,
      colors: {
        danger: { front: "#e53935", back: "#ffdedd" },
        primary: { front: "#42b983", back: "#c7e9da" },
        warning: { front: "#c25205", back: "#fed8bf" },
      },
    }
  },
  emits: ["stop-timer"],
  props: {
    delay: Number,
    color: String,
    stop: Boolean,
    pause: Boolean,
    timerIdx: Number,
  },
  mounted() {
    this.countDown()
  },
  methods: {
    countDown() {
      if (!this.isPaused) {
        if (this.timer > 0) {
          return setTimeout(() => {
            this.timer = this.timer - 10
            this.countDown()
          }, 10)
        } else {
          this.$emit("stop-timer")
        }
      }
    },
  },
  computed: {
    percent() {
      return (this.timer / this.delay) * 100 + "%"
    },
    frontColor() {
      return this.colors[this.color]["front"]
    },
    backColor() {
      return this.colors[this.color]["back"]
    },
    isPaused() {
      return this.pause
    },
  },
  watch: {
    pause: function (val) {
      if (!val) {
        this.countDown()
      }
    },
  },
}
</script>

<style>
</style>