<template>
  <div class='square' @click='coordinates'>
    <h1 v-if='robotIsPresent'>R</h1>
    <h1 v-if='targetIsPresent'>T</h1>
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
    }
  }
}
</script>

<style scoped>
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
}
</style>
