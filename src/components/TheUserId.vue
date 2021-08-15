<template>
  <div class="container">
    <div class="card">
      <div class="card">
        <h2>Поиск по ID</h2>
        <input type="text" id="uID" placeholder="ID"
               :class="inputState"
               :value="uid"
               @input="uIDHandler($event.target.value)"
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
      uid: undefined,
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
        const response = await fetch(`https://api.hl.ua/api/private/userid/${this.uid}/`, {
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
            if ('error' in payload && payload.error === error.notFound) {
              // console.log('NOT FOUND')
              this.inputState = 'notFound'
              this.payload = ''
            } else {
              this.inputState = 'found'
              this.payload = payload
            }
            break
          case 404:
            this.inputState = 'notFound'
            this.payload = ''
            break
          default:
            this.$emit('authBad')
            console.log('TheUserID() response:', response.statusText)
        }
      } catch (e) {
        this.$emit('authBad')
        console.log('TheUserID() catch:', e)
      }
    },
    uIDHandler (val) {
      this.uid = val
      this.whoAmIAPI()
    },
    validateForm () {
      const regex = new RegExp('^[0-9]{1,10}$')
      if (regex.test(this.uid)) {
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
