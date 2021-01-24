<template>
  <transition-group name="slide-top" appear tag="div">
    <div
      v-for="alert in alerts"
      :key="alert.id"
      :class="`alert ${alert.type}`"
      @mouseover="pause = true"
      @mouseleave="pause = false"
    >
      <div class="alert-container">
        <h3>{{ alert.title }}</h3>
        <p>{{ alert.text }}</p>
        <app-button
          :styled="alert.type + ' corner-btn'"
          @click.prevent="$emit('close', alert.id)"
          >X</app-button
        >
      </div>
      <app-timer
        :color="alert.type"
        :delay="alert.delay"
        :pause="pause"
        :timerIdx="alert.id"
        @stop-timer="$emit('close', alert.id)"
      ></app-timer>
    </div>
  </transition-group>
</template>

<script>
import AppButton from "./AppButton.vue"
import AppTimer from "./AppTimer.vue"
export default {
  data() {
    return {
      active: true,
      stop: false,
      pause: false,
    }
  },
  emits: ["close"],
  props: {
    alerts: Array,
  },
  components: { AppButton, AppTimer },
  methods: {},
  computed: {},
  watch: {},
}
</script>

<style>
</style>