<template>
  <h2>Robot Direction: {{directions[robotDirection]}}</h2>
  <p><strong>Player Score: </strong>{{playerScore}}</p>
  <p v-if="invalidCoordinates" class="error">Your robot has crashed off the board!!!</p>
  <div v-for="(row, rowIndex) in board" class='board-row' :key='rowIndex'>
    <div v-for="(square, squareIndex) in row" class='board-square' :key='squareIndex'>
      <Square 
        :robotIsPresent="robotIsPresent(squareIndex, rowIndex)"
        :targetIsPresent="targetIsPresent(squareIndex, rowIndex)"
        robotDirection="directions[robotDirection"
      />
    </div>
  </div>
  <div class="game-controls">
    <button @click="moveRobotForward">Move Forward</button>
    <div>
      <button @click="rotateRobotLeft">Rotate Left</button>
      <button @click="rotateRobotRight">Rotate Right</button>
    </div>
  </div>
</template>

<script>
import Square from '@/components/Square.vue'
export default {
  name: 'Board',
  components: {
    Square
  },
  props: {
    playerScore: {
      type: Number, 
      required: true
    }
  },
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
      targetLocationX: 3, 
      targetLocationY: 2,
      robotDirection: 0,
      directions: {0: 'Up', 1: 'Left', 2: 'Down', 3: 'Right'}
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
    scorePoint () {
      if (this.matchCoordinates) {
        this.regenerateTarget()
        this.$emit('scorePoint')
        return true
      } else {
        return false
      }
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
      while (this.matchCoordinates) {
        this.targetLocationX = this.generateRandomNumber(5)
        this.targetLocationY = this.generateRandomNumber(5)
      }
    }, 
    generateRandomNumber (max) {
      return Math.floor(Math.random() * max)
    }
  }
}
</script>

<style lang="scss" scoped>
.board-row {
  display: flex;
  justify-content: center;
}

.game-controls {
  padding-top: 10px;
}

.error {
  color: red;
}
</style>
