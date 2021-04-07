<template>
  <div class='board'>
    <Board 
      :robotLocation = 'robotLocation'
      :targetLocation = 'targetLocation'
    />
  </div>
</template>

<script>
import Board from '@/components/Board.vue'
export default {
  name: 'Game',
  components: {
    Board
  }, 
  data () {
    return {
      robotLocation: { 'x': 1, 'y': 2 },
      robotDirection: 'u',
      targetLocation: { 'x': 4, 'y': 2 }
    }
  }
}
</script>

<style scoped>
.board {
  padding-top: 100px;
}

</style>
