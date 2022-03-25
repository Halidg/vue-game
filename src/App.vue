<template>
  <div class="container">
    <game-vue></game-vue> 
  </div>
</template>

<script>
import GameVue from './components/GameVue.vue'

export default {
  components: {GameVue}
}
</script>

<style lang="scss" scoped>
.container {
  margin: 0 auto;
  padding: 15px;
  max-width: 900px;
}
</style>
