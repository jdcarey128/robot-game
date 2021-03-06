<template>
  <div class="game-wrapper">
    <div class='timer-wrapper'>
      <Timer 
        :timeLeft="timeLeft"
        :timeLimit="timeLimit"
        :warningThreshold="warningThreshold"
        :alertThreshold="alertThreshold"
        :gameActive="gameActive"
        :robotLife="robotLife"
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
        :boardLength="boardLength"
        :timePassed="timePassed"
        :timeLimit="timeLimit"
        :obstacleFrequency="obstacleFrequency"
        @scorePoint="scorePoint"
        @loseRobotLife="loseRobotLife"
      />
      <HighScorePopup v-if="highScore && !gameActive"
        :playerScore="playerScore"
        :boardSize="boardLength"
        @highScoreSubmitted="highScoreSubmitted"
      >
        <h2>🎉 New High Score!!! 🎉</h2>
      </HighScorePopup>
    </div>
    <div class="game-instructions">
      <h2>Instructions</h2>
      <select :disabled="gameActive" name="board-size" class="board-size" v-model="boardSelect">
        <option disabled value="">Select board size</option>
        <option>3 x 3</option>
        <option>5 x 5</option>
        <option>7 x 7</option>
        <option>9 x 9</option>
      </select>
      <p>Use the control buttons or the left, right, and up arrow keys to direct the robot to collect as many batteries as you can within the time limit. 
        The robot rotates 90 degrees from its perspective and moves in the direction it is facing (towards its blue shadow). <strong>Be careful to stay within the grid boundaries and 
        to avoid the water droplets!!!</strong></p>
    </div>
    <div class="leader-board">
      <LeaderBoard
        :key="componentKey"
        :playerScore="playerScore"
        :gameActive="gameActive"
        @newHighScore="newHighScore"
      />
    </div>
  </div>
</template>

<script>
import Board from '@/components/Board.vue'
import Timer from '@/components/Timer.vue'
import LeaderBoard from '@/components/LeaderBoard.vue'
import HighScorePopup from '@/components/HighScorePopup.vue'

export default {
  name: 'Game',
  components: {
    Board, Timer, LeaderBoard, HighScorePopup
  }, 
  data () {
    return {
      playerScore: 0,
      timeLimit: 60, 
      timePassed: 0,
      timerInterval: null, 
      warningThreshold: 20, 
      alertThreshold: 5, 
      robotLife: 4,
      highScore: false,
      componentKey: 0,
      boardLength: 5,
      boardSelect: '',
      obstacleFrequency: 5
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
    },
    boardSelect: function (x) {
      this.changeBoardLength(x)
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
      this.highScore = false
      this.timerInterval = null
      this.playerScore = 0
      this.timePassed = 0
      this.robotLife = 4
    }, 
    endGame () {
      clearInterval(this.timerInterval)
      this.timerInterval = null 
      this.timePassed = this.timeLimit
    },
    loseRobotLife () {
      this.robotLife -= 1
    },
    newHighScore () {
      this.highScore = true
    },
    highScoreSubmitted () {
      this.componentKey += 1
      this.highScore = false
    },
    changeBoardLength (newBoardLength) {
      this.boardLength = parseInt(newBoardLength)
    }
  }
}
</script>

<style lang="scss" scoped>
.game-wrapper {
  padding-top: 100px;
  align-items: center;
}
.timer-wrapper {
  margin-left: 10em;
  display: inline-block;
  width: calc(20% - 10em);
  vertical-align: middle;
}
.board-wrapper {
  display: inline-block;
  width: 50%;
  vertical-align: middle;
}
.game-instructions {
  display: inline-block;
  margin-right: 2em;
  width: calc(20% - 2em);
  vertical-align: middle;
}
.leader-board {
  margin-top: 100px;
}
</style>
