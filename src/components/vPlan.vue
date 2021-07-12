<template>
  <div class="plan" @drop="onDrop($event)" @dragover.prevent @dragenter.prevent>
    <h2 class="plan-title">
      {{ todoArr.title }} ({{ countArr() }})
    </h2>
    <div
      v-for="todo in todoArr.todo"
      :key="todo.id"
      class="plan-card"
      draggable="true"
      @dragstart="onDragStart($event, todo)"
    >
      <h3 class="plan-card__title">
        Задача №{{ todo.id }}
        <span
          :class="{
            low: todo.lvl == 1,
            medium: todo.lvl == 2,
            height: todo.lvl == 3,
          }"
          >{{ todo.lvl }}</span
        >
      </h3>
      <div class="plan-card__description">{{ todo.description }}</div>
      <p class="plan-card__data">{{ todo.date }}</p>
      <div class="plan-card__wrapper">
        <button
          class="plan-card__btn plan-card__btn__min"
          :disabled="disablePlan"
          @click="inLeft(todoArr, todo.id)"
        >
          ←
        </button
        ><button
          class="plan-card__btn"
          @click="
            $emit('editTask', { task: todo, id: todo.id, title: todoArr.title })
          "
        >
          Изменить</button><button class="plan-card__btn plan-card__btn__min" v-if="todoArr.title !== 'Готово'"
          @click="inRight(todoArr, todo.id)"
        >
           →
        </button>
        <button
          v-else
          class="plan-card__btn plan-card__btn__min"
          @click="deletTask(todo.id, todoArr)"
        >
          x
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      modalEdit: false,
      editLvl: null,
      editDesc: null,
    };
  },
  props: {
    todoArr: Object,
  },
  methods: {
    // e, todo
    onDragStart(e, todo) {
      e.dataTransfer.dropEffect = "move";
      e.dataTransfer.effectAllowed = "move";
      e.dataTransfer.setData(
        "todoId",
        this.todoArr.todo.map((e) => e.id).indexOf(todo.id)
      );
      e.dataTransfer.setData(
        "title",
        this.todoArr.title == "План"
          ? "plan"
          : this.todoArr.title == "Готово"
          ? "complited"
          : "progress"
      );
    },
    onDrop(e) {
      this.$emit("onDrop", {
        from: e.dataTransfer.getData("title"),
        id: e.dataTransfer.getData("todoId"),
        where:
          this.todoArr.title == "План"
            ? "plan"
            : this.todoArr.title == "Готово"
            ? "complited"
            : "progress",
      });
    },
    saveEdit(id) {
      for (let i = 0; i < this.todoArr.todo.length; i++)
        if (this.todoArr.todo[i].id === id) {
          this.todoArr.todo[i];
        }
    },
    inLeft(todoArr, id) {
      
      for (let i = 0; i < todoArr.todo.length; i++) {
        if (todoArr.todo[i].id == id) {
          this.$emit("inLeft", {
            title: todoArr.title,
            id: i,
          });
        }
      }
    },
    inRight(todoArr, id) {
      for (let i = 0; i < todoArr.todo.length; i++) {
        if (todoArr.todo[i].id == id) {
          this.$emit("inRight", {
            title: todoArr.title,
            id: i,
          });
        }
      }
    },
    countArr() {
      return this.todoArr.todo.length;
    },
    deletTask(id, todo) {
      todo.todo = todo.todo.filter((item) => item.id !== id);
      localStorage.setItem("complited", JSON.stringify(todo.todo));
    },
    toLeft() {
      if (this.todoArr.title === "Готово") {
        return true;
      } else {
        return false;
      }
    },
  },
  computed: {
    disablePlan() {
      if (this.todoArr.title === "План") {
        return true;
      } else {
        return false;
      }
    },
  },
};
</script>


