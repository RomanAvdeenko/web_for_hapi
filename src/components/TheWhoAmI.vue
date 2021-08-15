<template>
  <div class="container">
    <div class="card">
      <div>
        <strong>Мой логин: </strong>
        {{ payload }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      payload: ''
    }
  },
  emits: ['authBad'],
  methods: {
    async whoAmIAPI () {
      const response = await fetch('https://api.hl.ua/api/private/whoami', {
        method: 'GET',
        headers: {
          'content-type': 'application/json'
        },
        cache: 'no-cache'
      })
      if (response.status === 200) {
        try {
          this.payload = await response.json()
        } catch (e) {
          this.$emit('authBad')
          console.log('WhoAmi() catch:', e)
        }
        // console.log('Response OK')
      } else {
        this.$emit('authBad')
        console.log('WhoAmI() response:', response.statusText)
      }
    }
  },
  created () {
    this.whoAmIAPI()
  }
}
</script>

<style scoped>

</style>
