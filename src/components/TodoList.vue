<template>
  <div>
    <input type="text" class="todo-input"
      placeholder="Enter your task"
      v-model="newTodo" @keyup.enter="addTodo">
    <!-- TODO LIST -->
    <div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item">
        <div class="todo-item-left">
            <input type="checkbox" v-model="todo.completed">
            <div v-if="!todo.editing" @dblclick="editTodo(todo)"
            class="todo-item-label">
                {{todo.title}}
            </div>
            <input v-else class="todo-item-edit" type="text" v-model="todo.title"
            @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)" v-focus>
        </div>

        <div class="remove-item" @click="removeTodo(index)">
            &times;
        </div>
    </div>

    <div class="extra-container">
        <div>
            <button :class="{ active: filter == 'all'}" @click="filter = 'all'">All</button>
            <button :class="{ active: filter == 'active'}" @click="filter = 'active'">Active</button>
            <button :class="{ active: filter == 'completed'}" @click="filter = 'completed'">Completed</button>
        </div>

        <div>
            <button v-if="showClearButton" @click="clearCompleted">Clear</button>
        </div>
    </div>

  </div>
</template>

<script>
const STORAGE_KEY = 'Todo App Storage';
export default {
  name: "todo-list",
  //   props: {
  //     newTodo: String
  //   }
  data() {
    return {
      newTodo: '',
      idForTodo: 1,
      beforeEditCache: '',
      filter: 'all',
      //editing: false,
      todos: []
        //   {
        //       'id':1,
        //       'title': 'Start Vue',
        //       'completed': false,
        //       'editing': false,
        //   },
        //   {
        //       'id':2,
        //       'title': 'Finish Vue',
        //       'completed': false,
        //       'editing': false,
        //   },
      //]
    }
  },

  created () {
      this.todos = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]');
  },

  computed: {
      todosFiltered() {
          if(this.filter == 'all') {
              return this.todos
          } else if(this.filter == 'active') {
              return this.todos.filter(todo => !todo.completed)
          } else if(this.filter == 'completed') {
              return this.todos.filter(todo => todo.completed)
          }
          return this.todos
      },
      showClearButton() {
          return this.todos.filter(todo => todo.completed).length > 0
      }
  },

  directives: {
      focus: {
          inserted: function (el) {
              el.focus()
          }
      }
  },

  methods: {
      addTodo() {
        //   alert('adding')
        if(this.newTodo.trim().length == 0) {
            return
        }

        this.todos.push({
            id: this.idForTodo,
            title: this.newTodo,
            completed: false,
        })

        this.newTodo = ''
        this.idForTodo++

        localStorage.setItem(STORAGE_KEY, JSON.stringify(this.todos));
      },

      removeTodo(index) {
          this.todos.splice(index, 1)
          localStorage.setItem(STORAGE_KEY, JSON.stringify(this.todos));
      },

      editTodo(todo) {
          this.beforeEditCache = todo.title
          todo.editing = true
          
          localStorage.setItem(STORAGE_KEY, JSON.stringify(this.todos));
      },

      doneEdit(todo) {
          if(todo.title.trim() == '') {
            todo.title = this.beforeEditCache
        }
          todo.editing = false

          localStorage.setItem(STORAGE_KEY, JSON.stringify(this.todos));
      },

      cancelEdit(todo) {
          todo.title = this.beforeEditCache
          todo.editing = false
      },

      clearCompleted() {
          this.todos = this.todos.filter(todo => !todo.completed)
      }
  }
}
</script>

<style>
.todo-input {
  width: 100%;
  padding: 10px 18px;
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

.todo-item-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    font-family: 'Avenir', Arial, Helvetica, sans-serif;

    &:focus {
        outline: none;
    }
}

.extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgrey;
    padding-top: 14px;
    margin-bottom: 14px;
}

button {
    font-size: 14px;
    background-color: white;
    appearance: none;

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
</style>
