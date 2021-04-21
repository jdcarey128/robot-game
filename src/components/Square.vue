<template>
  <div :class="{
    'square':(!robotIsPresent),'square-robot':(robotIsPresent),'square-target':(targetIsPresent)
    }" 
    @click='coordinates'
  >
    <div :class="`robot-direction_${robotDirection}`" v-if='robotIsPresent'>
      <img class="robot-image" :src="robotImage" alt="robot">
    </div>
    <div class="square-target" v-if='targetIsPresent && !robotIsPresent'>
      <img class="target-image" :src="targetImage" alt="target">
    </div>
  </div>
</template>

<script>
import robotImage from '@/assets/floating-robot.png'
import targetImage from '@/assets/target-battery.png'

export default {
  name: 'Square',
  setup () {
    return {
      robotImage,
      targetImage
    }
  },
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
  display: flex;
  align-items: center;
  justify-content: center;
  float: left;
  height: 50px;
  width: 50px;
  margin-right: -1px;
  margin-top: -1px;
  background-color: white;
  border: 1px solid #2c3e50;
  padding: 0px;
  font-size: 30px;
  line-height: 0px;
  font-weight: bold;
}

.square-robot {
  @extend .square;
}

.square-target {
  display: flex;
  justify-content: center;
  align-items: center;
}

.robot-image {
  height: 50px;
}

.target-image {
  width: 60%;
}

.robot-direction_Up {
  transform: rotate(180deg);
}
.robot-direction_Left {
  transform: rotate(90deg);
}
.robot-direction_Right {
  transform: rotate(-90deg);
}

</style>
