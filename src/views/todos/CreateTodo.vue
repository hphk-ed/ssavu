<template>
  <div>
    <input type="text" v-model.trim="title" @keyup.enter="createTodo">
    <p><input type="file" id="file" @change="upload"><label for="file">+</label></p>
    <button @click="createTodo">+</button>
  </div>
</template>

<script>
import axios from'axios'

const SERVER_URL = process.env.VUE_APP_SERVER_URL

export default {
  name: 'CreateTodo',
  data: function () {
    return {
      title: '',
      img: '',
    }
  },
  methods: {
    getToken: function () {
      const config = {
        Authorization: `JWT ${localStorage.getItem('jwt')}`
      }
      return config
    },
    createTodo: function () {
      const todoItem = new FormData()
      todoItem.append('title', this.title)
      todoItem.append('img', this.img)
      // for (let k of fd.values()) {
      //   console.log(k)
      // }

      // const todoItem = {
      //   title: this.title,
      //   img: fd
      // }
      let config = this.getToken()
      config = {
        ...config,
        'content-type': 'multipart/form-data'
      }
      
      if (this.title) {
        axios({
          method: 'post',
          // url: 'http://127.0.0.1:8000/todos/',
          url: `${SERVER_URL}/todos/`,
          data: todoItem,
          headers: config,
        })
          .then((res) => {
            console.log(res)
            this.$router.push({ name: 'TodoList' }).catch(()=>{});
          })
          .catch((err) => {
            console.log(err.response)
          })
        }
    },
    upload: function(e) {
      // console.log(e.target.files[0])
      this.img = e.target.files[0]

      // let reader = new FileReader()
      // console.log(file)
      // reader.readAsDataURL(file[0])
      // reader.onload = e => {
      //   // console.log(e.target.result)
      //   this.img = e.target.result
      //   // axios({
      //   //   method: 'post',
      //   //   url: 'http://127.0.0.1:8000/todos/',
      //   //   data: e.target.result,
      //   // })
      //   //   .then(res => {
      //   //     console.log(res)
      //   //   })
      //   //   .catch(err => {
      //   //     console.log(err)
      //   //   })
      // }
    }
  }
}
</script>
