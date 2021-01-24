<template>
  <button class="btn" :class="styled" @click="$emit('buttonclick')">
    <slot />
  </button>
</template>

<script>
export default {
  emits: ["buttonclick"],
  props: {
    styled: {
      type: String,
      default: "",
      validator(value) {
        let arr = value.split(" ")
        return [
          "",
          "primary",
          "warning",
          "danger",
          "delete",
          "edit",
          "drag",
          "corner-btn",
        ].some((v) => arr.indexOf(v) !== -1)
      },
    },
  },
}
</script>

<style lang="stylus" scoped></style>