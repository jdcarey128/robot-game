<template>
  <h3>Robot Direction: {{directions[robotDirection]}}</h3>
  <div class="game-values">
    <p class="value"><strong>Player Score: </strong>{{playerScore}}</p>
    <p class="value"><strong>Robot Life: </strong>{{robotLife}}</p>
  </div>
  <p v-if="invalidCoordinates && robotAlive" class="error">Your robot crashed off the board and lost a life!!!</p>
  <button class="button-regenerate" v-if="invalidCoordinates && robotAlive" @click="resetRobot">Regenerate Robot</button>
  <p v-if="!robotAlive" class="error">Your robot is destroyed!!!<br><strong>GAME OVER</strong></p>
  <div v-for="(row, rowIndex) in board" class='board-row' :key='rowIndex'>
    <div v-for="(square, squareIndex) in row" class='board-square' :key='squareIndex'>
      <Square 
        :robotIsPresent="robotIsPresent(squareIndex, rowIndex)"
        :targetIsPresent="targetIsPresent(squareIndex, rowIndex)"
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

export default {
  name: 'Board',
  setup () {
    return {
      rotateArrow
    }
  },
  components: {
    Square
  },
  mounted () {
    window.addEventListener('keyup', event => {
      if (this.gameActive) {
        if (event.keyCode === 37) {
          this.rotateRobotLeft()
        } else if (event.keyCode === 39) {
          this.rotateRobotRight()
        } else if (event.keyCode === 38) {
          this.moveRobotForward()
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
    }
  },
  emits: ["scorePoint", "loseRobotLife"],
  data () {
    return {
      board:[
        [0, 1, 2, 3, 4],
        [0, 1, 2, 3, 4],
        [0, 1, 2, 3, 4],
        [0, 1, 2, 3, 4],
        [0, 1, 2, 3, 4]
      ], 
      robotLocationX: 2,
      robotLocationY: 2,
      targetLocationX: null, 
      targetLocationY: null,
      robotDirection: 2,
      directions: {0: 'Up', 1: 'Left', 2: 'Down', 3: 'Right'}
    }
  },
  watch: {
    gameActive: function () {
      if (this.gameActive) {
        this.regenerateTarget()
      }
    },
    gameReset: function () {
      if (this.gameReset) {
        this.resetTarget()
        this.resetRobot()
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
    }
  },
  computed: {
    invalidX () {
      return this.robotLocationX < 0 || this.robotLocationX > 4
    },
    invalidY () {
      return this.robotLocationY < 0 || this.robotLocationY > 4
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
    matchCoordinates () {
      return this.matchXCoordinates && this.matchYCoordinates
    },
    robotAlive () {
      return this.robotLife > 0
    }
  }, 
  methods: {
    robotIsPresent (xIndex, yIndex) {
      return xIndex === this.robotLocationX && yIndex === this.robotLocationY
    },
    targetIsPresent (xIndex, yIndex) {
      return xIndex === this.targetLocationX && yIndex === this.targetLocationY
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
      this.targetLocationX = this.generateRandomNumber(5)
      this.targetLocationY = this.generateRandomNumber(5)
      while (this.matchCoordinates) {
        this.targetLocationX = this.generateRandomNumber(5)
        this.targetLocationY = this.generateRandomNumber(5)
      }
    }, 
    resetTarget () {
      this.targetLocationX = null 
      this.targetLocationY = null
    },
    resetRobot () {
      this.robotLocationX = 2
      this.robotLocationY = 2
      this.robotDirection = 2
    },
    generateRandomNumber (max) {
      return Math.floor(Math.random() * max)
    }
  }
}
</script>

<style lang="scss" scoped>
.game-values {
  display: flex;
  justify-content: center;
  .value {
    margin: 10px 15px;
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
