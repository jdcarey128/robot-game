<template>
  <p><strong>Robot Direction: </strong>{{directions[robotDirection]}}</p>
  <div class="game-values">
    <p class="value"><strong>Player Score: </strong>{{playerScore}}</p>
    <p class="value"><strong>Robot Battery Life: </strong></p>
    <span class="robot-battery-life">
      <img v-if="robotLife === 4" class="robot-battery-life" :src="fullHealth" :alt="robotLife">
      <img v-if="robotLife === 3" class="robot-battery-life" :src="threeHealth" :alt="robotLife">
      <img v-if="robotLife === 2" class="robot-battery-life" :src="twoHealth" :alt="robotLife">
      <img v-if="robotLife === 1" class="robot-battery-life" :src="oneHealth" :alt="robotLife">
      <img v-if="robotLife === 0" class="robot-battery-life" :src="deadBattery" :alt="robotLife">
    </span>
  </div>
  <p v-if="invalidCoordinates && robotAlive" class="error">Your robot crashed off the board and lost battery life!!!</p>
  <button class="button-regenerate" v-if="invalidCoordinates && robotAlive" @click="resetRobot">Regenerate Robot</button>
  <p v-if="obstacleHealthLost && robotAlive" class="error">Your robot lost battery life!!!</p>
  <p v-if="!robotAlive" class="error">Your robot is drained!!!<br><strong>GAME OVER</strong></p>
  <div v-for="(row, rowIndex) in generatedBoard" class='board-row' :key='rowIndex'>
    <div v-for="(square, squareIndex) in row" class='board-square' :key='squareIndex'>
      <Square 
        :robotIsPresent="robotIsPresent(squareIndex, rowIndex)"
        :targetIsPresent="targetIsPresent(squareIndex, rowIndex)"
        :obstacleIsPresent="obstacleIsPresent(squareIndex, rowIndex)"
        :robotDirection="directions[robotDirection]"
      />
    </div>
  </div>
  <div class="game-controls">
    <button :disabled='!gameActive' @click="moveRobotForward">Move Forward</button>
    <div>
      <button :disabled='!gameActive' @click="rotateRobotLeft"><img class="rotate-arrow-left" :src="rotateArrow" alt="Rotate Left"></button>
      <button :disabled='!gameActive' @click="rotateRobotRight"><img class="rotate-arrow" :src="rotateArrow" alt="Rotate Right"></button>
    </div>
  </div>
</template>

<script>
import Square from '@/components/Square.vue'
import rotateArrow from '@/assets/rotate-arrow.png'
import fullHealth from '@/assets/green-battery.png'
import threeHealth from '@/assets/yellow-battery.png'
import twoHealth from '@/assets/orange-battery.png'
import oneHealth from '@/assets/red-battery.png'
import deadBattery from '@/assets/dead-battery.png'

export default {
  name: 'Board',
  setup () {
    return {
      rotateArrow,
      fullHealth,
      threeHealth,
      twoHealth,
      oneHealth,
      deadBattery
    }
  },
  components: {
    Square
  },
  mounted () {
    window.addEventListener('keydown', (event) => {
      if (this.gameActive) {
        if (event.key === 'ArrowLeft') {
          this.rotateRobotLeft()
        } else if (event.key === 'ArrowRight') {
          this.rotateRobotRight()
        } else if (event.key === 'ArrowUp') {
          event.preventDefault()
          this.moveRobotForward()
        } else if (event.key === 'ArrowDown') {
          event.preventDefault()
        }
      }
    })
  },
  props: {
    playerScore: {
      type: Number, 
      required: true
    },
    gameActive: {
      type: Boolean, 
      required: true
    }, 
    gameReset: {
      type: Boolean, 
      required: true
    }, 
    robotLife: {
      type: Number, 
      required: true
    },
    boardLength: {
      type: Number, 
      required: true
    }, 
    timePassed: {
      type: Number, 
      required: true
    },
    timeLimit: {
      type: Number,
      required: true
    },
    obstacleFrequency: {
      type: Number,
      required: true
    }
  },
  emits: ["scorePoint", "loseRobotLife"],
  data () {
    return {
      robotLocationX: 2,
      robotLocationY: 2,
      targetLocationX: null, 
      targetLocationY: null,
      robotDirection: 2,
      directions: {0: 'Up', 1: 'Left', 2: 'Down', 3: 'Right'},
      obstacleTimer: 0,
      obstacleInterval: null,
      obstacleLocationX: null,
      obstacleLocationY: null,
      obstacleHealthLost: false,
    }
  },
  watch: {
    gameActive: function () {
      if (this.gameActive) {
        this.regenerateTarget()
      } else {
        this.resetObstacle()
      }
    },
    gameReset: function () {
      if (this.gameReset) {
        this.resetTarget()
        this.resetRobot()
        this.resetObstacle()
      }
    }, 
    invalidCoordinates: function () {
      if (this.invalidCoordinates) {
        this.$emit('loseRobotLife')
      }
    }, 
    matchCoordinates: function () {
      if (this.matchCoordinates) {
        this.regenerateTarget()
        this.$emit('scorePoint')
      }
    },
    boardLength: function () {
      this.resetRobot()
    },
    timePassed: function () {
      if (!this.obstacleOnBoard && (this.generateRandomNumber(this.timeLimit) < this.timePassed)) {
        this.generateObstacle()
      }
    },
    matchObstacleRobotCoordinates: function () {
      if (this.matchObstacleRobotCoordinates) {
        this.resetObstacle()
        this.toggleObstacleHealth()
        this.$emit('loseRobotLife')
        setTimeout(() => {
          this.toggleObstacleHealth()
        }, 2000)
      }
    },
    obstacleTimer: function () {
      if (this.obstacleTimer > this.obstacleFrequency) {
        this.resetObstacle()
      }
    }
  },
  computed: {
    invalidX () {
      return this.robotLocationX < 0 || this.robotLocationX > this.boardLength - 1
    },
    invalidY () {
      return this.robotLocationY < 0 || this.robotLocationY > this.boardLength - 1
    }, 
    invalidCoordinates () {
      return this.invalidX || this.invalidY
    }, 
    matchXCoordinates () {
      return this.robotLocationX === this.targetLocationX
    },
    matchYCoordinates () {
      return this.robotLocationY === this.targetLocationY
    },
    obstacleMatchRobotXCoordinates () {
      return this.robotLocationX === this.obstacleLocationX 
    },
    obstacleMatchRobotYCoordinates () {
      return this.robotLocationY === this.obstacleLocationY 
    },
    obstacleMatchTargetXCoordinates () {
      return this.obstacleLocationX === this.targetLocationX
    },
    obstacleMatchTargetYCoordinates () {
      return this.obstacleLocationY === this.targetLocationY
    },
    matchCoordinates () {
      return this.matchXCoordinates && this.matchYCoordinates
    },
    matchObstacleRobotCoordinates () {
      return this.obstacleMatchRobotXCoordinates && this.obstacleMatchRobotYCoordinates
    },
    matchObstacleTargetCoordinates () {
      return this.obstacleMatchTargetXCoordinates && this.obstacleMatchTargetYCoordinates
    },
    robotAlive () {
      return this.robotLife > 0
    },
    generatedBoard () {
      return Array.from(new Array(this.boardLength), () => Array.from(new Array(this.boardLength), (x, index) => index ))
    },
    obstacleOnBoard () {
      return !!this.obstacleLocationX || !!this.obstacleLocationY
    }
  }, 
  methods: {
    robotIsPresent (xIndex, yIndex) {
      return xIndex === this.robotLocationX && yIndex === this.robotLocationY
    },
    targetIsPresent (xIndex, yIndex) {
      return xIndex === this.targetLocationX && yIndex === this.targetLocationY
    },
    obstacleIsPresent (xIndex, yIndex) {
      return xIndex === this.obstacleLocationX && yIndex === this.obstacleLocationY
    },
    moveRobotForward () {
      if (this.robotDirection === 0) {
        return this.robotLocationY -= 1
      } else if (this.robotDirection === 1) {
        return this.robotLocationX -= 1
      } else if (this.robotDirection === 2) {
        return this.robotLocationY += 1
      } else 
        return this.robotLocationX += 1
    },
    rotateRobotLeft () {
      if (this.robotDirection < 3) {
        return this.robotDirection += 1
      } else {
        return this.robotDirection = 0
      }
    },
    rotateRobotRight () {
      if (this.robotDirection > 0) {
        return this.robotDirection -= 1
      } else {
        return this.robotDirection = 3
      }
    },
    regenerateTarget () {
      this.targetLocationX = this.generateRandomNumber(this.boardLength)
      this.targetLocationY = this.generateRandomNumber(this.boardLength)
      while (this.matchCoordinates || this.matchObstacleTargetCoordinates) {
        this.targetLocationX = this.generateRandomNumber(this.boardLength)
        this.targetLocationY = this.generateRandomNumber(this.boardLength)
      }
    }, 
    resetTarget () {
      this.targetLocationX = null 
      this.targetLocationY = null
    },
    resetRobot () {
      this.robotLocationX = Math.floor(this.boardLength / 2) 
      this.robotLocationY = Math.floor(this.boardLength / 2) 
      this.robotDirection = 2
    },
    resetObstacle () {
      clearInterval(this.obstacleInterval)
      this.obstacleInterval = null
      this.obstacleTimer = 0
      this.obstacleLocationX = null 
      this.obstacleLocationY = null
    },
    generateRandomNumber (max) {
      return Math.floor(Math.random() * max)
    },
    generateObstacle () {
      clearInterval(this.obstacleInterval)
      this.obstacleLocationX = this.generateRandomNumber(this.boardLength)
      this.obstacleLocationY = this.generateRandomNumber(this.boardLength)
      while (this.matchObstacleRobotCoordinates || this.matchObstacleTargetCoordinates) {
        this.obstacleLocationX = this.generateRandomNumber(this.boardLength)
        this.obstacleLocationY = this.generateRandomNumber(this.boardLength)
      }
      this.startObstacleTimer()
    },
    startObstacleTimer () {
      this.obstacleInterval = setInterval(() => (this.obstacleTimer += 1), 1000)
    },
    toggleObstacleHealth () {
      this.obstacleHealthLost = !this.obstacleHealthLost
      console.log('obstacle health', this.obstacleHealthLost)
    }
  }
}
</script>

<style lang="scss" scoped>
.game-values {
  display: flex;
  justify-content: center;
  .value {
    margin: 0px 15px;
    margin-bottom: 10px;
  }
  .robot-battery-life {
    width: 14px;
  }
}
.button-regenerate {
  color: red;
  padding: 2px;
  margin-top: 0px;
}
.board-row {
  display: flex;
  justify-content: center;
}

.game-controls {
  padding-top: 10px;
  .rotate-arrow-left {
    width: 20px;
    transform: scaleX(-1);
  }
  .rotate-arrow {
    width: 20px;
  }
}

.error {
  color: red;
  margin: 0px;
}
</style>
