<template v-cloack>
  <div class="alert-box" v-if="alerts.length !== 0">
    <app-alert :alerts="alerts" @close="closeAlert"></app-alert>
  </div>
  <div class="container column">
    <transition appear name="slide-fade">
      <form class="card card-w30">
        <app-select
          :types="types"
          :current="currentType"
          @block_type_change="changeT"
          >Тип блока</app-select
        >

        <app-text-area
          @button_active="changeButtonActivity"
          :tArea="area"
          :rowsNum="rowsNum"
          >Значение</app-text-area
        >

        <app-button
          styled="primary"
          :disabled="disabled"
          @click.prevent="addBlock"
          >Добавить</app-button
        >
        <app-button
          styled="primary"
          :disabled="false"
          @click.prevent="postResume"
          >Сохранить</app-button
        >
        <app-button styled="" :disabled="false" @click.prevent="loadResume"
          >Сбросить</app-button
        >
        <app-button
          styled="warning"
          :disabled="false"
          @click.prevent="pushAlert"
          >Случайное уведомление</app-button
        >
      </form>
    </transition>

    <div class="card card-w70">
      <template v-if="blocks.length !== 0">
        <transition-group appear name="slide-top" tag="div">
          <component
            v-for="block in blocks"
            :key="`${block.content}${block.type}`"
            :is="'app-' + block.type"
            :componentId="block.id"
            :editable="editable"
            :type="block.type"
            :content="block.content"
            :over="over"
            @alert="showAlert"
            @delete="deleteItem"
            @edit="editItem"
            @drop="onDrop($event, block.id)"
            draggable="false"
            @dragover.prevent
            @dragenter.prevent
            @dragenter="dragOver(block.id)"
            @dragend="dragLeave(block.id)"
          ></component>
        </transition-group>
      </template>

      <h3 v-else>Добавьте первый блок, чтобы увидеть результат</h3>
    </div>
  </div>

  <div class="container">
    <AppCommentsList
      @load="loadComments"
      :comments="comments"
      title="Комментарии"
    ></AppCommentsList>
    <app-loader v-if="loading"></app-loader>
  </div>
</template>

<script>
import axios from "axios"
import AppButton from "./components/AppButton.vue"
import AppSelect from "./components/AppSelect.vue"
import AppLoader from "./components/AppLoader.vue"
import AppHeader from "./components/AppHeader.vue"
import AppAvatar from "./components/AppAvatar.vue"
import AppTextArea from "./components/AppTextArea.vue"
import AppSemiheader from "./components/AppSemiheader.vue"
import AppText from "./components/AppText.vue"
import AppAlert from "./components/AppAlert.vue"
import AppCommentsList from "./components/AppCommentsList.vue"
export default {
  props: {},
  data() {
    return {
      comments: [],
      alerts: [],
      loading: false,
      disabled: true,
      content: "",
      area: "",
      key: "",
      editable: false,
      rowsNum: 3,
      currentType: "header",
      drag: false,
      over: -1,
      blocks: [],
      types: [
        { title: "Заголовок", arg: "header" },
        { title: "Подзаголовок", arg: "semiheader" },
        { title: "Аватар", arg: "avatar" },
        { title: "Текст", arg: "text" },
      ],
    }
  },
  mounted() {
    this.loadResume()
  },
  methods: {
    dragOver(e) {
      this.over = e
    },
    dragLeave(e) {
      this.over = -1
    },
    onDrop(e, item) {
      this.over = -1
      const itemId = e.dataTransfer.getData("ItemId")
      if (e && itemId) {
        Array.prototype.move = function (from, to) {
          let el = this.splice(from - 1, 1)[0]
          el.id = to
          if (from < to) {
            this.forEach(function (i) {
              if (i.id > from && i.id <= to) {
                i.id--
              }
            })
          } else if (to < from) {
            this.forEach(function (i) {
              if (i.id < from && i.id >= to) {
                i.id++
              }
            })
          }
          this.splice(to - 1, 0, el)
          return this
        }
        this.blocks.move(itemId, item)
      }
    },
    async loadComments() {
      this.loading = true

      try {
        const { data } = await axios.get(
          "https://jsonplaceholder.typicode.com/comments?_limit=42.json"
        )
        if (!data) {
          throw new Error("Список комментариев пуст")
        }

        this.comments = Object.keys(data).map((key) => {
          return {
            id: key,
            ...data[key],
          }
        })
        this.alerts.push({
          id: this.alertsCounter,
          type: "primary",
          title: "Обращение к БД",
          text: "Комментарии успешно загружены",
          delay: 2500,
        })
        this.loading = false
      } catch (e) {
        this.alerts.push({
          id: this.alertsCounter,
          type: "danger",
          title: "Ошибка!",
          text: e.message,
          delay: 3000,
        })
        this.loading = false
        console.log(e.message)
      }
    },
    async loadResume() {
      this.loading = true

      try {
        const { data } = await axios.get(
          "https://vue-2nd-week-default-rtdb.firebaseio.com/resume.json"
        )
        if (!data) {
          throw new Error("Резюме пустое")
        }

        this.blocks = data
        this.alerts.push({
          id: this.alertsCounter,
          type: "primary",
          title: "Обращение к БД",
          text: "Резюме успешно загружено",
          delay: 2500,
        })
        this.loading = false
      } catch (e) {
        this.alerts.push({
          id: this.alertsCounter,
          type: "danger",
          title: "Ошибка!",
          text: e.message,
          delay: 2500,
        })
        this.loading = false
        console.log(e.message)
      }
    },
    async postResume() {
      this.loading = true
      const resume = JSON.stringify(this.blocks)
      try {
        const { data } = await axios.put(
          "https://vue-2nd-week-default-rtdb.firebaseio.com/resume.json",
          resume
        )
        this.alerts.push({
          id: this.alertsCounter,
          type: "primary",
          title: "Обращение к БД",
          text: "Резюме сохранено",
          delay: 2500,
        })
        this.loading = false
      } catch (e) {
        this.alerts.push({
          id: this.alertsCounter,
          type: "danger",
          title: "Ошибка!",
          text: e.message,
          delay: 2000,
        })
        this.loading = false
        console.log(e.message)
      }
    },
    pushAlert() {
      function getRandomInt(min, max) {
        return Math.round(Math.random() * (max - min)) + min
      }
      let warn = {
        1: { type: "danger", text: "Ошибка!" },
        2: { type: "primary", text: "Уведомление" },
        3: { type: "warning", text: "Предупреждение?" },
      }
      let mode = getRandomInt(1, 3)
      let delay = getRandomInt(5000, 10000)
      this.alerts.push({
        id: this.alertsCounter,
        type: warn[mode]["type"],
        title: warn[mode]["text"],
        text: "Исчезнет автоматически за " + delay + " милисекунд",
        delay: delay,
      })
    },
    closeAlert(idx) {
      this.alerts.forEach((elem, i) => {
        if (elem.id == idx) {
          this.alerts.splice(i, 1)
        }
      })
    },
    deleteItem(id) {
      this.blocks.splice(id - 1, 1)
      this.blocks.forEach(function (item) {
        if (item.id > id) {
          item.id--
        }
      })
    },
    editItem(id, text) {
      if (text.length > 3) {
        for (let elem in this.blocks) {
          if (this.blocks[elem].id == id) {
            this.blocks[elem].content = text
          }
        }
      }
    },
    addBlock() {
      this.blocks.push({
        id: this.bloksCounter,
        type: this.currentType,
        content: this.content,
      })
      this.currentType = "header"
      this.area = ""
      this.disabled = true
    },
    changeT(data) {
      this.currentType = data
    },
    changeButtonActivity(data, data2) {
      this.disabled = data
      this.content = data2
      this.area = data2
    },

    showAlert(type, title, text, delay = 0) {
      this.alerts.push({
        id: this.alertsCounter,
        type: type,
        title: title,
        text: text,
        delay: delay,
      })
    },
  },
  components: {
    AppButton,
    AppCommentsList,
    AppLoader,
    AppSelect,
    AppHeader,
    AppAvatar,
    AppSemiheader,
    AppText,
    AppTextArea,
    AppAlert,
  },
  computed: {
    bloksCounter() {
      return this.blocks.length + 1
    },
    alertsCounter() {
      let arr = this.alerts.map((el) => {
        return el.id
      })
      return arr.length > 0 ? Math.max.apply(null, arr) + 1 : 1
    },
    showAlertsArray() {
      return this.alerts
    },
  },
}
</script>

<style>
.avatar {
  display: flex;
  justify-content: center;
}

.avatar img {
  width: 150px;
  height: auto;
  border-radius: 50%;
}
</style>
