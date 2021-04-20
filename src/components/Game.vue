<template>
  <div class='timer-wrapper'>
    <Timer 
      :timeLeft="timeLeft"
      :timeLimit="timeLimit"
      :warningThreshold="warningThreshold"
      :alertThreshold="alertThreshold"
      :gameActive="gameActive"
      @startTimer="startTimer"
      @resetGame="resetGame"
    />
  </div>
  <div class='board-wrapper'>
    <Board 
      :playerScore="playerScore"
      :gameActive="gameActive"
      :gameReset="gameReset"
      :robotLife="robotLife"
      @scorePoint="scorePoint"
      @loseRobotLife="loseRobotLife"
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
      timeLimit: 20, 
      timePassed: 0,
      timerInterval: null, 
      warningThreshold: 15, 
      alertThreshold: 5, 
      robotLife: 2
    }
  },
  watch: {
    timePassed: function () {
      if (this.timePassed >= this.timeLimit) {
        this.endGame()
      }
    }, 
    robotLife: function () {
      if (this.robotLife <= 0) {
        this.endGame()
      }
    }
  },
  computed: {
    timeLeft () {
      return this.timeLimit - this.timePassed
    }, 
    gameActive () {
      return this.timerInterval != null 
    }, 
    gameReset () {
      return this.playerScore === 0 && this.timerInterval === null
    }
  },
  methods: {
    scorePoint () {
      this.playerScore += 1
    },
    startTimer () {
      this.timerInterval = setInterval(() => (this.timePassed += 1), 1000)
    },
    resetGame () {
      clearInterval(this.timerInterval)
      this.timerInterval = null
      this.playerScore = 0
      this.timePassed = 0
      this.robotLife = 2
    }, 
    endGame () {
      clearInterval(this.timerInterval)
      this.timerInterval = null
      this.timePassed = this.timeLimit
    },
    loseRobotLife () {
      this.robotLife -= 1
    }
  }
}
</script>

<style scoped>
.timer-wrapper {
  display: inline-block;
  vertical-align: middle;
}
.board-wrapper {
  display: inline-block;
  margin: 100px 100px;
  vertical-align: middle;
}

</style>
