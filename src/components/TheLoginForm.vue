<template>
  <div class="container">
    <form class="card center" @submit.prevent="loginAPI">
      <div class="form-control">
        <label for="login">Login:</label>
        <input v-focus type="text" id="login" v-model="login" placeholder="логин">
        &nbsp; <label for="password">Password:</label>
        <input type="password" id="password" v-model="password" placeholder="пароль">
        <button class="btn" :class="btnClass" :disabled="buttonState">OK</button>
        <div>
          <slot></slot>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
import focusDirective from '@/directives/focus'

const passwordMinLength = 6

export default {
  data () {
    return {
      login: '',
      password: '',
      btnClass: 'primary'
    }
  },
  emits: ('authOK'),
  methods: {
    async loginAPI () {
      const response = await fetch('https://api.hl.ua/api/login', {
        method: 'POST',
        headers: {
          'content-type': 'application/json'
        },
        cache: 'no-cache',
        body: JSON.stringify({
          login: this.login,
          password: this.password
        })
      })
      if (response.status === 200) {
        this.$emit('authOK')
        // console.log('User authenticated')
      } else {
        console.log('Bad login or password')
        this.btnClass = 'danger'
        setTimeout(() => {
          this.btnClass = 'primary'
        }, 100)
      }
      // console.log(response)
    }
  },
  computed: {
    buttonState () {
      return this.login.length < 1 || this.password.length < passwordMinLength
    }
  },
  directives: {
    focus: focusDirective
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
