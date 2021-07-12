<template>
  <main class="main">
    <v-popup v-if="modal"  v-on:close="close">
      <div class="popup-form">
        <button @click="close" class="popup-close">
          x
        </button>
        <label for="description">Описание</label>
        <input
          type="text"
          v-model="modalTitle"
          name="description"
          class="popup-field"
        />
        <label for="lvl">Приоритет</label>
        <select name="lvl" class="popup-field" v-model="modalLvl">
          <option value="1" class="popup-select">1</option>
          <option value="2" class="popup-select">2</option>
          <option value="3" class="popup-select">3</option>
        </select>
        <button
          @click="save(tasks.plan.todo)"
          v-if="!editBool"
          class="popup-save"
        >
          Сохранить
        </button>
        <button v-else @click="saveEdit" class="popup-save">
          Внести изменения
        </button>
      </div>
    </v-popup>
    <div class="container">
      <button
        @click="
          modal = true;
          modalTitle = '';
          modalLvl = 1;
        "
        class="main-btn"
      >
        Добавить задачу
      </button>
      <div class="main-wrapper">
        <v-plan
          v-for="(item, key) in tasks"
          :key="key"
          :todoArr="item"
          v-on:inLeft="inLeft"
          v-on:inRight="inRight"
          v-on:editTask="editTask"
          v-on:onDrop="onDrop"
        />
      </div>
    </div>
  </main>
</template>

<script>
import vPlan from "./vPlan";
import vPopup from "./vPopup";
export default {
  data() {
    return {
      modal: false,
      modalid: null,
      modalTitle: "",
      editBool: false,
      editTitle: null,
      left: null,
      storagePlan: localStorage.setItem("plan", 1),
      storageProgress: localStorage.setItem("progress", 1),
      storageComplited: localStorage.setItem("complited", 1),
      modalLvl: 1,
      countTasks: +localStorage.getItem("countTasks") || 0,
      tasks: {
        plan: {
          title: "План",
          todo: JSON.parse(localStorage.getItem("plan")).todo || [],
        },
        progress: {
          title: "В работе",
          todo: JSON.parse(localStorage.getItem("progress")).todo || [],
        },
        complited: {
          title: "Готово",
          todo: JSON.parse(localStorage.getItem("complited")).todo || [],
        },
      },
    };
  },
  components: {
    vPlan,
    vPopup,
  },
  methods: {
    onDrop(value) {
      this.tasks[value.where].todo.push(this.tasks[value.from].todo[value.id]);
      this.tasks[value.from].todo.splice(value.id, 1);
      localStorage.setItem(
        `${value.where}`,
        JSON.stringify(this.tasks[value.where])
      );
      localStorage.setItem(
        `${value.from}`,
        JSON.stringify(this.tasks[value.from])
      );
    },
    saveEdit() {
      switch (this.editTitle) {
        case "План":
          this.tasks.plan.todo.forEach((item) => {
            if (item.id === this.modalid) {
              item.description = this.modalTitle;
              item.lvl = this.modalLvl;
              return;
            }
          });
          this.modal = false;
          localStorage.plan = JSON.stringify(this.tasks.plan);
          break;
        case "В работе":
          this.tasks.progress.todo.forEach((item) => {
            if (item.id === this.modalid) {
              item.description = this.modalTitle;
              item.lvl = this.modalLvl;
              return;
            }
          });
          this.modal = false;
          localStorage.setItem("progress", JSON.stringify(this.tasks.progress));
          break;
        case "Готово":
          this.tasks.complited.todo.forEach((item) => {
            if (item.id === this.modalid) {
              item.description = this.modalTitle;
              item.lvl = this.modalLvl;
              return;
            }
          });
          this.modal = false;
          localStorage.setItem(
            "complited",
            JSON.stringify(this.tasks.complited)
          );
          break;
      }
    },
    onKeyEscape(e){
      if (e.key==="Escape"){
        this.close()
      }
    },
    editTask(value) {
      this.modalLvl = value.task.lvl;
      this.modalTitle = value.task.description;
      this.editBool = true;
      this.modal = true;
      this.editTitle = value.title;
      this.modalid = value.id;
    },
    inLeft(value) {
      if (value.title === "Готово") {
        this.tasks.progress.todo.push(this.tasks.complited.todo[value.id]);
        this.tasks.complited.todo.splice(value.id, 1);
        localStorage.setItem("complited", JSON.stringify(this.tasks.complited));
      } else {
        this.tasks.plan.todo.push(this.tasks.progress.todo[value.id]);
        this.tasks.progress.todo.splice(value.id, 1);
        localStorage.plan = JSON.stringify(this.tasks.plan);
      }
      localStorage.setItem("progress", JSON.stringify(this.tasks.progress));
    },
    inRight(value) {
      if (value.title === "План") {
        this.tasks.progress.todo.push(this.tasks.plan.todo[value.id]);
        this.tasks.plan.todo.splice(value.id, 1);
        localStorage.plan = JSON.stringify(this.tasks.plan);
      } else {
        this.tasks.complited.todo.push(this.tasks.progress.todo[value.id]);
        this.tasks.progress.todo.splice(value.id, 1);
        localStorage.setItem("complited", JSON.stringify(this.tasks.complited));
      }
      localStorage.setItem("progress", JSON.stringify(this.tasks.progress));
    },
    save(arr) {
      this.countTasks += 1;
      const newTodo = {
        id: this.countTasks,
        lvl: this.modalLvl,
        description: this.modalTitle,
        date: this.formatDate(new Date()),
      };
      arr.push(newTodo);
      localStorage.setItem(
        "plan",
        JSON.stringify({ title: "План", todo: arr })
      );
      this.modalLvl = 1;
      this.modalTitle = "";
      this.modal = false;
    },
    close() {
      this.modal = false;
      this.editBool = false;
      this.modalTitle = "";
      this.modalLvl = 1;
    },
    formatDate(date) {
			const monthNames = [
				'Января', 'Февраля', 'Марта',
				'Апреля', 'Мая', 'Июня', 'Июля',
				'Августа', 'Сентября', 'Октября',
				'Ноября', 'Декабря',
			];

			const h = date.getHours() >= 10 ? date.getHours() : '0' + date.getHours();
			const m = date.getMinutes() >= 10 ? date.getMinutes() : '0' + date.getMinutes();
			const day = date.getDate();
			const monthIndex = date.getMonth();
			const year = date.getFullYear();

			return `${ h }:${ m } ${ day } ${ monthNames[monthIndex] } ${ year }`;
		}
  },
  watch: {
    countTasks() {
      localStorage.setItem("countTasks", this.countTasks)
    },
  },
  created(){
     document.addEventListener('keydown', this.onKeyEscape);
  }
};

</script>
