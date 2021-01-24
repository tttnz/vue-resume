<template>
  <div>
    <div class="card" v-if="this.comments.length !== 0">
      <h2>{{ title }}</h2>
      <ul class="list">
        <AppComment :comments="comments"></AppComment>
      </ul>
    </div>
    <p v-else>
      <app-button styled="primary" @click="$emit('load')"
        >Загрузить {{ title }}</app-button
      >
    </p>
  </div>
</template>

<script>
import AppButton from "./AppButton.vue"
import AppComment from "./AppComment.vue"
export default {
  emits: ["load"],
  props: {
    comments: Array,
    title: {
      type: String,
      required: true,
    },
  },
  components: { AppComment, AppButton },
}
</script>