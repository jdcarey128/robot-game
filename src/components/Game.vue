<template>
  <div class='board-wrapper'>
    <Board 
      :playerScore="playerScore"
      @scorePoint="scorePoint"
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
      playerScore: 0
    }
  },
  methods: {
    scorePoint () {
      this.playerScore += 1
    }
  }
}
</script>

<style scoped>
.board-wrapper {
  padding-top: 100px;
}

</style>
