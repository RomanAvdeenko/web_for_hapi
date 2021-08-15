<template>
  <div class="container">
    <div class="card">
      <div class="card">
        <h2>Поиск по логину</h2>
        <input type="text" id="uLogin" placeholder="логин"
               :class="inputState"
               :value="uLogin"
               @input="uLoginHandler($event.target.value)"
        >
      </div>
      <div class="card" :class="(inputState!=='found')?'disabled':''">
        <div class="card-s">
          <strong>ID: </strong>{{ payload.id }}
        </div>
        <div class="card-s">
          <strong>Login: </strong>{{ payload.login }}
        </div>
        <div class="card-s">
          <label for="chEnabled">
            <input type="checkbox" id="chEnabled" v-model="payload.enabled">
            Включен
          </label>
        </div>
        <div class="card-s">
          <strong>ФИО/Организация: </strong>{{ payload.name }}
        </div>
        <div class="card-s">
          <strong>Баланс: </strong>{{ payload.balance }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import error from '@/Errors'

export default {
  data () {
    return {
      uLogin: '',
      payload: '',
      inputState: 'invalid'
      // 'valid', 'invalid', 'found', 'notFound'
    }
  },
  emits: ['authBad'],
  methods: {
    async whoAmIAPI () {
      if (!this.validateForm()) return
      try {
        const response = await fetch(`https://api.hl.ua/api/private/user/${this.uLogin}/`, {
          method: 'GET',
          headers: {
            'content-type': 'application/json'
          },
          cache: 'no-cache'
        })

        let payload = ''
        switch (response.status) {
          case 200:
            payload = await response.json()
            // console.log('payload', payload)
            // console.log('payload.error', payload.error)
            if ('error' in payload && payload.error === error.notFound) {
              // console.log('NOT FOUND')
              this.inputState = 'notFound'
              this.payload = ''
            } else {
              // console.log('FOUND:')
              this.inputState = 'found'
              this.payload = payload
              // console.log('Response OK')
            }
            break
          case 404:
            this.inputState = 'notFound'
            this.payload = ''
            break
          default:
            this.$emit('authBad')
            console.log('TheUserLogin() response:', response.statusText)
        }
      } catch (e) {
        this.$emit('authBad')
        console.log('TheUserLogin() catch:', e)
      }
    },
    uLoginHandler (val) {
      this.uLogin = val
      this.whoAmIAPI()
    },
    validateForm () {
      const regex = new RegExp('^[a-zA-z0-9]{5,256}$')
      if (regex.test(this.uLogin)) {
        this.inputState = 'valid'
        return true
      }
      this.inputState = 'invalid'
      this.payload = ''
      return false
    }
  },
  computed: {},
  created () {
    this.whoAmIAPI()
  }
}
</script>

<style scoped>

</style>
