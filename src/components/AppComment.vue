<template>
  <transition-group appear name="slide-top">
    <li class="list-item" v-for="comm in comments" :key="comm.id">
      <div>
        <h3>{{ comm.name }}</h3>
        <p>
          <strong>{{ comm.email }}</strong>
        </p>
        <small>{{ comm.body }}</small>
      </div>
    </li>
  </transition-group>
</template>

<script>
export default {
  emits: ["buttonclick"],
  props: {
    comments: Array,
    styled: {
      type: String,
      default: "",
      validator(value) {
        return ["", "primary", "danger"].includes(value)
      },
    },
  },
}
</script>

<style scoped>
</style>