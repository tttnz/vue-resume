<template>
  <div class="form-control">
    <label for="value"><slot /></label>
    <textarea
      id="value"
      :rows="rowsNum"
      v-model="tArea"
      @input="onInput"
    ></textarea>
  </div>
</template>

<script>
export default {
  emits: ["button_active"],
  props: {
    tArea: String,
    rowsNum: {
      type: Number,
      default: 3,
      required: false,
    },
  },
  computed: {
    onInput() {
      this.$emit("button_active", this.tArea.length <= 3, this.tArea)
    },
  },
}
</script>

<style>
</style>