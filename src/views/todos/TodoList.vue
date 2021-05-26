<template>
  <div>
    <ul>
      <li v-for="(todo, idx) in todos" :key="idx">
        <span @click="updateTodoStatus(todo)" :class="{ completed: todo.completed }">{{ todo.title }}</span>
        <button @click="deleteTodo(todo)" class="todo-btn">X</button>
      </li>
    </ul>
    <!-- <button @click="getTodos">Get Todos</button> -->
  </div>
</template>

<script>
import axios from 'axios'

const SERVER_URL = process.env.VUE_APP_SERVER_URL

export default {
  name: 'TodoList',
  data: function () {
    return {
      todos: [],
    }
  },
  methods: {
    getToken: function () {
      const config = {
        Authorization: `JWT ${localStorage.getItem('jwt')}`
      }
      return config
    },
    getTodos: function () {
      axios({
        method: 'get',
        // url: 'http://127.0.0.1:8000/todos/',
        url: `${SERVER_URL}/todos/`,
        headers: this.getToken(),
      })
        .then((res) => {
          console.log(res)
          this.todos = res.data
        })
        .catch((err) => {
          console.log(err)
        })
    },
    deleteTodo: function (todo) {
      axios({
        method: 'delete',
        // url: `http://127.0.0.1:8000/todos/${todo.id}/`,
        url: `${SERVER_URL}/todos/${todo.id}/`,
        headers: this.getToken(),
      })
        .then((res) => {
          console.log(res)
          this.getTodos()
        })
        .catch((err) => {
          console.log(err)
        })
    },
    updateTodoStatus: function (todo) {
      const img = todo.img ? todo.img : ''
      const todoItem = {
        ...todo,
        img,
        completed: !todo.completed
      }

      console.log(todoItem)
      axios({
        method: 'put',
        url: `${SERVER_URL}/todos/${todo.id}/`,
        data: todoItem,
        headers: this.getToken(),
      })
        .then((res) => {
          console.log(res)
          todo.completed = !todo.completed
        })
        .catch(err => {
          console.log(err.response)
        })
      },
    },
  created: function () {
    if (localStorage.getItem('jwt')){
      this.getTodos()
    } else {
      this.$router.push({ name: 'Login' }).catch(()=>{});
    }
  }
}
</script>

<style scoped>
  .todo-btn {
    margin-left: 10px;
  }

  .completed {
    text-decoration: line-through;
    color: rgb(112, 112, 112);
  }
</style>
