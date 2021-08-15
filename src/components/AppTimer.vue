<template>
  <slot></slot>
  {{ currentTime }}
</template>

<script>
let timer

export default {
  data () {
    return {
      currentTime: new Date().toLocaleTimeString()
    }
  },
  mounted () {
    this.timer = setInterval(() => {
      this.currentTime = new Date().toLocaleTimeString()
    }, 500)
  },
  beforeUnmount () {
    clearInterval(timer)
  }
}
</script>

<style scoped>

</style>
