<template>
  <div v-bind:class="{'square':(!robotIsPresent),'square-robot':(robotIsPresent), 'square-target':(targetIsPresent)}" @click='coordinates'>
    <p v-if='robotIsPresent'>R</p>
    <p v-if='targetIsPresent'>T</p>
  </div>
</template>

<script>
export default {
  name: 'Square',
  props: {
    robotIsPresent: {
      type: Boolean,
      required: true
    },
    targetIsPresent: {
      type: Boolean,
      required: true
    },
    robotDirection: {
      type: String, 
      required: true
    }
  }
}
</script>

<style lang="scss" scoped>
.square {
  float: left;
  height: 50px;
  width: 50px;
  margin-right: -1px;
  margin-top: -1px;
  background-color: white;
  border: 1px solid #2c3e50;
  padding: 0px;
  text-align: center;
  font-size: 30px;
  line-height: 0px;
  font-weight: bold;
}

.square-robot {
  @extend .square;
  background-color: lightcyan;
}

.square-target {
  @extend .square;
  background-color: lightyellow;
}

</style>
