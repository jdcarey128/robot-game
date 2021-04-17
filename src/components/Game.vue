<template>
  <div class='timer-wrapper'>
    <Timer 
      :timeLeft="timeLeft"
      :timeLimit="timeLimit"
      :warningThreshold="warningThreshold"
      :alertThreshold="alertThreshold"
    />
  </div>
  <button @click="startTimer">Start Timer</button>
  <div class='board-wrapper'>
    <Board 
      :playerScore="playerScore"
      @scorePoint="scorePoint"
    />
  </div>
</template>

<script>
import Board from '@/components/Board.vue'
import Timer from '@/components/Timer.vue'

export default {
  name: 'Game',
  components: {
    Board, Timer
  }, 
  data () {
    return {
      playerScore: 0,
      timeLimit: 5, 
      timePassed: 0,
      timerInterval: null, 
      warningThreshold: 15, 
      alertThreshold: 5
    }
  },
  computed: {
    timeLeft () {
      return this.timeLimit - this.timePassed
    }
  },
  methods: {
    scorePoint () {
      this.playerScore += 1
    },
    startTimer () {
      this.timerInterval = setInterval(() => (this.timePassed += 1), 1000)
    }
  }
}
</script>

<style scoped>
.board-wrapper {
  padding-top: 100px;
}

</style>
