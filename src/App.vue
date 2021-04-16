<template>
  <div class="game-wrapper">
    <Game />
  </div>
</template>

<script>
import Game from '@/components/Game.vue'

export default {
  name: 'App',
  components: {
    Game
  }
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-color: #F3F5FA;
  min-height: 100vh;
}
</style>
