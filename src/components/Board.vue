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
  props: {
    size: {
      type: String, 
      required: true
    },
    robotLocation: {
      type: Object, 
      required: true
    },
    targetLocation: {
      type: Object,
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
      ]
    }
  },
  computed: {
    robotReachedTarget () {
      if (this.robotLocation['x'] === this.targetLocation['x'] && this.robotLocation['y'] === this.targetLocation['y']) {
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
      return xIndex === this.robotLocation['x'] && yIndex === this.robotLocation['y']
    },
    targetIsPresent (xIndex, yIndex) {
      return xIndex === this.targetLocation['x'] && yIndex === this.targetLocation['y']
    }
  }
  
}
</script>

<style scoped>
.board-row {
  display: flex;
  justify-content: center;
}
</style>
