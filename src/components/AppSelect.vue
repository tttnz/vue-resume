<template>
  <div class="form-control">
    <label for="type"><slot /></label>
    <select id="type" v-model="current" @change="changeType">
      <option v-for="item in types" :key="item.arg" :value="item.arg">
        {{ item.title }}
      </option>
    </select>
  </div>
</template>

<script>
export default {
  emits: ["block_type_change"],
  props: {
    types: Array,
    current: {
      type: String,
      required: true,
      default: "header",
      validator(value) {
        return ["semiheader", "avatar", "header", "text"].includes(value)
      },
    },
  },
  methods: {
    changeType() {
      this.$emit("block_type_change", this.current)
    },
  },
}
</script>

<style>
</style>
