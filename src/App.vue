<template>
  <nav>
    <h1 class="title">Robot Grid</h1>
  </nav>
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
nav {
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: rgba(94, 219, 241, 0.2);
  width: 100%;
  border-bottom: 1px solid #ccc;
  
  .title {
    color: #ccc;
    font-weight: bold;
    font-size: 40px;
    padding: 0px 100px;
    -webkit-text-stroke-width: 0.5px;
    -webkit-text-stroke-color: rgb(94, 219, 241);
  }
}
</style>
