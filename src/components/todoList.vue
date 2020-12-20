<template>
  <div>
    <input
      type="text"
      class="todo-input"
      placeholder="What needs to bo done"
      v-model="newTodo"
      @keyup.enter="addTodo"
    />
    <transition-group
      name="group"
      enter-active-class="animate__animated animate__fadeInUp"
      leave-active-class="animate__animated animate__fadeOutDown"
    >
      <div
        v-for="(todo, index) in todosFiltered"
        :key="todo.id"
        class="todo-item"
      >
        <div class="todo-item-left">
          <input type="checkbox" v-model="todo.completed" />
          <div
            v-if="!todo.editing"
            @dblclick="editTodo(todo)"
            class="todo-item-label"
            :class="{ completed: todo.completed }"
          >
            {{ todo.title }}
          </div>
          <input
            v-else
            class="todo-item-edit"
            type="text"
            v-model="todo.title"
            @blur="doneEdit(todo)"
            @keyup.enter="doneEdit(todo)"
            @keyup.esc="cencelEdit(todo)"
            v-focus
          />
        </div>
        <div class="remove-item" @click="removeTodo(index)">
          &times;
        </div>
      </div>
    </transition-group>

    <div class="extra-container">
      <div>
        <label
          ><input
            type="checkbox"
            :checked="!anyRemaining"
            @change="checkAllTodos"
          />Check All</label
        >
      </div>
      <div>{{ remaining }} items left</div>
    </div>
    <div class="extra-container">
      <div>
        <button :class="{ active: filter == 'all' }" @click="filter = 'all'">
          All
        </button>
        <button
          :class="{ active: filter == 'active' }"
          @click="filter = 'active'"
        >
          Active
        </button>
        <button
          :class="{ active: filter == 'completed' }"
          @click="filter = 'completed'"
        >
          completed
        </button>
      </div>
      <div>
        <transition name="fade">
          <button v-if="showClearCompletedButton" @click="clearCompleted">
            Clear Completed
          </button>
        </transition>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "todo-list",
  data() {
    return {
      newTodo: "",
      idForTodo: 3,
      beforEditCache: "",
      filter: "all",
      todos: [
        {
          id: 1,
          title: "Finish Vue Screencast",
          completed: false,
          editing: false,
        },
        { id: 2, title: "Take over world", completed: false, editing: false },
      ],
    };
  },
  computed: {
    remaining() {
      return this.todos.filter((todo) => todo.completed === false).length;
    },
    anyRemaining() {
      return this.remaining !== 0;
    },
    todosFiltered() {
      if (this.filter == "active") {
        return this.todos.filter((todo) => todo.completed === false);
      } else if (this.filter == "completed") {
        return this.todos.filter((todo) => todo.completed === true);
      } else {
        return this.todos;
      }
    },
    showClearCompletedButton() {
      return this.todos.filter((todo) => todo.completed === true).length > 0;
    },
  },
  directives: {
    focus: {
      // directive definition
      mounted(el) {
        el.focus();
      },
    },
  },
  methods: {
    addTodo() {
      if (this.newTodo.trim() == "") {
        return;
      }

      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false,
      });

      this.newTodo = "";
      this.idForTodo++;
    },
    removeTodo(index) {
      this.todos.splice(index, 1);
    },
    editTodo(todo) {
      this.beforEditCache = todo.title;
      todo.editing = true;
    },
    doneEdit(todo) {
      if (todo.title.trim() == "") {
        todo.title = this.beforEditCache;
        return;
      }
      todo.editing = false;
    },
    cencelEdit(todo) {
      todo.title = this.beforEditCache;
      todo.editing = false;
    },
    checkAllTodos(e) {
      this.todos.forEach((todo) => (todo.completed = e.target.checked));
    },
    clearCompleted() {
      this.todos = this.todos.filter((todo) => !todo.completed);
    },
  },
};
</script>

<style lang="scss">
@import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.compat.css");

.todo-input {
  width: 100%;
  padding: 10px 10px;
  font-size: 18px;
  margin-bottom: 16px;

  &:focus {
    outline: 0;
  }
}

.todo-item {
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.remove-item {
  cursor: pointer;
  margin-left: 14px;
  &:hover {
    color: black;
  }
}

.todo-item-left {
  display: flex;
  align-items: center;
}

.todo-item-label {
  padding: 10px;
  border: 1px solid white;
  margin-left: 12px;
}

.todo-item-edit {
  font-size: 24px;
  color: #2c3e50;
  margin-left: 12px;
  padding: 10px;
  border: 1px solid #ccc;
  font-family: "Aveniz", Helvetica, Arial, sans-serif;

  &:focus {
    outline: none;
  }
}

.completed {
  text-decoration: line-through;
  color: gray;
}

.extra-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 16px;
  border-top: 1px solid lightgray;
  padding-top: 14px;
  margin-bottom: 14px;
}

button {
  font-size: 14px;
  background-color: white;
  border: 1px solid lightgray;
  appearance: none;
  margin-right: 5px;

  &:hover {
    background: lightgreen;
  }

  &:focus {
    outline: none;
  }
}
.active {
  background: lightgreen;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity.2s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>
