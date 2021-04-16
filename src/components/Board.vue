<template>
  <div v-for="(row, rowIndex) in board" class='board-row' :key='rowIndex'>
    <div v-for="(square, squareIndex) in row" class='board-square' :key='squareIndex'>
      <Square 
        :robotIsPresent='robotIsPresent(squareIndex, rowIndex)'
        :targetIsPresent='targetIsPresent(squareIndex, rowIndex)'
      />
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
  data () {
    return {
      board:[
        [0, 1, 2, 3, 4],
        [0, 1, 2, 3, 4],
        [0, 1, 2, 3, 4],
        [0, 1, 2, 3, 4],
        [0, 1, 2, 3, 4]
      ], 
      robotLocationX: 0,
      robotLocationY: 1,
      targetLocationX: 3, 
      targetLocationY: 2,
      robotDirection: 'u'
    }
  },
  computed: {
    robotReachedTarget () {
      if (this.robotLocationX === this.targetLocationX && this.robotLocationY === this.targetLocationY) {
        alert('true')
        return 1
      } else {
        alert('false')
        return 1
      }
    }
  }, 
  methods: {
    robotIsPresent (xIndex, yIndex) {
      return xIndex === this.robotLocationX && yIndex === this.robotLocationY
    },
    targetIsPresent (xIndex, yIndex) {
      return xIndex === this.targetLocationX && yIndex === this.targetLocationY
    }
  }
  
}
</script>

<style lang="scss" scoped>
.board-row {
  display: flex;
  justify-content: center;
}
</style>
