<template>
  <div class="app" id="app">
  <form class="form" v-on:submit="addTodo">
    <input class="input form-input" placeholder="Adicionar tarefa!" v-model="inputVal"/>
    <button class="btn form-submit-btn" type="submit">Adicionar</button>
  </form>
  <transition-group tag="ol" name="list" class="todo-list">
    <li class="todo-list-item" v-bind:class="{ complete: todo.complete }"
      v-bind:key="index" v-for="(todo, index) in filteredTodos">
      <button class="todo-list-item-content" v-on:click="toggleTodo(todo)">
        {{ todo.text }}
      </button>
      <button class="btn todo-list-item-remove" v-on:click="deleteTodo(index)">
        <i class="fa" v-bind:class="[todo.complete ? 'fa-check' : 'fa-times']"></i>
      </button>
    </li>
  </transition-group>
  <div class="filters">
    <button 
      class="btn filters-btn filters-btn--all" v-on:click="filterTodos('all')">
      Todas
    </button>
    <button 
      class="btn filters-btn filters-btn--complete" v-on:click="filterTodos('complete')">
      Concluídas
    </button>
    <button 
      class="btn filters-btn filters-btn--incomplete" v-on:click="filterTodos('incomplete')">
      Por fazer
    </button>
  </div>
</div>
</template>

<script>
  var filters = {
  all: function(todos) {
    return todos;
  },
  complete: function(todos) {
    return todos.filter(function(todo) {
      return todo.complete;
    });
  },
  incomplete: function(todos) {
    return todos.filter(function(todo) {
      return !todo.complete;
    });
  }
}

var STORAGE_KEY = 'vue-js-todo-P7oZi9sL'
var todoStorage = {
  fetch: function () {
    var todos = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]');
    return todos;
  },
  save: function (todos) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todos));
  }
}

export default ({
  el: '#app',
  data() {
    return {
      inputVal: '',
      todos: todoStorage.fetch(),
      visibility: 'all'
    }
  },
  watch: {
    todos: {
      handler: function(todos) {
        todoStorage.save(todos);
      }
    }
  },
  computed: {
    filteredTodos: function () {
      return filters[this.visibility](this.todos);
    }
  },
  methods: {
    addTodo: function(e) {
      e.preventDefault();
      if (this.inputVal) {
        this.todos.push({
          text: this.inputVal,
          complete: false
        });
      }
      this.inputVal = '';
    },
    toggleTodo: function(todo) {
      todo.complete = !todo.complete;
    },
    filterTodos: function(filter) {
      this.visibility = filter;
    },
    deleteTodo: function(index) {
      this.todos.splice(index, 1);
    }
  }
});
</script>

<style lang="scss" scoped>
$color-main: #4fc08d;

$font-family: 'FontAwesome', sans-serif;

*, *:before, *:after {
  box-sizing: border-box;
}

html {
  background: linear-gradient(210deg, lighten($color-main, 20%), $color-main);
  font-feature-settings: "liga", "kern";
  height: 100%;
  overflow-y: scroll;
  overflow-x: hidden; 
}

body {
  align-items: center;
  display: flex;
  font-family: $font-family;
  height: 100%;
  justify-content: center;
  overflow: hidden;
}

button {
  background: none;
  border: none;
  color: inherit;
  font-size: inherit;
  font-weight: inherit;
  &:focus {
    outline: none;
  }
  &:hover {
    cursor: pointer;
  }
}

.app {
  align-items: center;
  background: #fff;
  border-radius: 1em;
  box-shadow: 0 0 5px rgba(25,25,25,.25);
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  min-height: 50vh;
  overflow: hidden;
}

.btn {
  background: none;
  border: 1px solid;
  border-radius: 2em;
  border: $color-main 1px solid;
  color: #4fc08d;
  color: darken($color-main, 20%);
  cursor: pointer;
  font-family: $font-family;
  font-size: 14px;
  letter-spacing: 1px;
  margin: 0 .5em;
  padding: 0.75em 1.5em;
  transition: 250ms ease-out;
  &:hover, &:focus {
    color: #fff;
    background: $color-main;
  }
}

.form {
  display: flex;
  padding: 1.5rem 1rem 0 1rem;
  width: 100%;
  &-input {
    background: none;
    border: #e3e3e3 1px solid;
    border-radius: 2em;
    font-family: $font-family;
    font-size: 14px;
    margin: 0 .5em;
    padding: 0.75em 1.5em;
    transition: border 250ms ease-out;
    width: 100%;
    &:focus {
      border: $color-main 1px solid;
      outline: none;
    }
  }
}

.todo-list {
  $block: #{&};
  flex: 1;
  padding: 0 1rem;
  width: 100%;
  &-item {
    align-items: center;
    border-radius: 3px;
    color: $color-main;
    display: flex;
    justify-content: space-between;
    margin-bottom: .5em;
    padding: .5em;
    transition: 200ms;
    &:last-child { margin-bottom: 0; }
    &.complete { 
      color: #e3e3e3;
      #{$block}-item-content {
        &:after {
          background: #e3e3e3;
        }
      }
    }
  }
  &-item-content {
    position: relative;
    &:after {
      background: $color-main;
      content: "";
      height: 1px;
      left: 0;
      position: absolute;
      right: 0;
      transform: scalex(0);
      transform-origin: center;
      transition: 250ms ease-out;
      top: 100%;
    }
    &:hover, &:focus {
      &:after {
        transform: scalex(1);
      }
    }
  }
  &-item-remove {
    align-items: center;
    background: none;
    border: 1px solid;
    border-radius: 50%;
    color: inherit;
    display: flex;
    font-size: 80%;
    height: 2em;
    line-height: 1;
    justify-content: center;
    margin-left: .5em;
    padding: 0;
    width: 2em; 
    &:hover, &:focus {
      background: #ffbdbd;
      color: #ff0000;
    }
  }
}

.filters {
  display: flex;
  justify-content: space-around;
  padding: 0 1rem 1.5rem 1rem;
  width: 100%;
}

.list-move,
.list-leave-active,
.list-enter-active {
  transition: 500ms cubic-bezier(.87,-.41,.19,1.44);
}
.list-enter,
.list-leave-active {
  opacity: 0;
  transform: translate(100%, 0);
}
</style>