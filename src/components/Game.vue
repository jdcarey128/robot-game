<template>
  <div class='board-wrapper'>
    <Board />
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
    }
  }
}
</script>

<style scoped>
.board-wrapper {
  padding-top: 100px;
}

</style>
