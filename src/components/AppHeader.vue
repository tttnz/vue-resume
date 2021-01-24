<template>
  <div
    :class="'component-block ' + isOver"
    draggable
    @dragStart="onDragStart($event, componentId)"
  >
    <div v-if="editable">
      <p class="hint">
        CTRL+Enter для подтверждения (больше 3-х символов). ESC — для отмены
      </p>
      <app-text-area
        @button_active="changeButtonActivity2"
        :tArea="content"
        @keyup.esc="editable = !editable"
        @keyup.ctrl.enter="$emit('edit', componentId, content2)"
      ></app-text-area>
    </div>
    <h1 v-else>{{ content }}</h1>
    <div class="corner">
      <app-button v-if="!editable" styled="drag" :disabled="false"
        ><img src="../assets/drag.svg" alt=""
      /></app-button>
      <app-button
        v-if="!editable"
        styled="edit"
        :disabled="false"
        @click="editable = !editable"
        ><img src="../assets/edit.svg" alt=""
      /></app-button>
      <app-button
        v-else
        styled="edit"
        :disabled="editable2"
        @click="$emit('edit', componentId, content2)"
        ><img src="../assets/ok.svg" alt=""
      /></app-button>
      <app-button
        styled="delete"
        :disabled="false"
        @click="$emit('delete', componentId)"
        ><img src="../assets/trash.svg" alt=""
      /></app-button>
    </div>
  </div>
</template>

<script>
import AppButton from "./AppButton.vue"
import AppTextArea from "./AppTextArea.vue"
export default {
  data() {
    return {
      editable2: false,
      content2: this.content,
    }
  },
  emits: ["edit", "delete"],
  props: {
    over: Number,
    editable: Boolean,
    componentId: {
      type: Number,
      required: true,
    },
    content: String,
    drag: Boolean,
  },
  methods: {
    changeButtonActivity2(data, data2) {
      this.editable2 = data
      this.content2 = data2
    },
    onDragStart(e, item) {
      e.dataTransfer.dropEffect = "move"
      e.dataTransfer.effectAllowed = "move"
      e.dataTransfer.setData("ItemId", item)
    },
  },
  computed: {
    isOver() {
      return this.over == this.componentId ? "overed" : " "
    },
  },
  components: { AppButton, AppTextArea },
}
</script>