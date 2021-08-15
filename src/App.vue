<template>
  <div class="container" v-if="!isAuth">
    <the-login-form
      @authOK="isAuth=true"
    >
      <app-timer><strong>current time:</strong></app-timer>
    </the-login-form>
  </div>
  <div class="container" v-else>
    <the-who-am-i
      @authBad="isAuth=false"
    >
    </the-who-am-i>
    <the-user-id
      @authBad="isAuth=false"
    >
    </the-user-id>
    <the-user-login
      @authBad="isAuth=false"
    >
    </the-user-login>
  </div>
</template>

<script>
import TheWhoAmI from '@/components/TheWhoAmI'
import TheLoginForm from '@/components/TheLoginForm'
import TheUserId from '@/components/TheUserId'
import TheUserLogin from '@/components/TheUserLogin'
// import AppUserForm from '@/components/AppUserForm'
import AppTimer from '@/components/AppTimer'

let timer

export default {
  data () {
    return {
      isAuth: false
    }
  },
  methods: {
    isLoginCookieExist () {
      return document.cookie.startsWith('hitline_billing_session=')
    }
  },
  async created () {
    // Проверяем есть ли cookie
    if (this.isLoginCookieExist()) this.isAuth = true
  },
  beforeUnmount () {
    clearInterval(timer)
  },
  components: {
    TheWhoAmI,
    TheLoginForm,
    TheUserId,
    TheUserLogin,
    AppTimer
    // AppUserForm
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
