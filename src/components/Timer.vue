<template>
  <p class="alert" v-if="!gameActive && timeLeft==0"><strong>Time's Up!!!</strong></p>
  <div class="timer">
    <svg
      class="timer_svg"
      viewBox="0 0 100 100"
      xmlns="http://www.w3.org/2000/svg"
    >
      <g class="timer_circle">
        <circle 
          class="timer_path-elapsed"
          cx="50"
          cy="50"
          r="46.5"
        />
        <path 
          :stroke-dasharray="circleDasharray"
          :class="remainingPathColor"
          class="timer_path-remaining"
          d="
            M 50, 50
            m -45, 0
            a 45,45 0 1,0 90,0
            a 45,45 0 1,0 -90,0
          "
        ></path>
      </g>
    </svg>
    <span class="timer_label">
      {{ formattedTimeLeft }}
    </span>
    <div class=timer-buttons>
      <button :disabled='disableStart' class='button_game-start' @click="startGame">Start</button>
      <button class='button_game-reset' @click="resetGame">Reset</button>
    </div>
  </div>
</template>

<script>
export default {
  emits: ["startTimer", "resetGame"],
  props: {
    timeLeft: {
      type: Number, 
      required: true
    },
    timeLimit: {
      type: Number, 
      required: true
    }, 
    warningThreshold: {
      type: Number, 
      default: 15
    },
    alertThreshold: {
      type: Number, 
      default: 5
    },
    gameActive: {
      type: Boolean, 
      required: true
    }
  },
  computed: {
    formattedTimeLeft() {
      const timeLeft = this.timeLeft 
      if (timeLeft > 0) {
        const minutes = Math.floor(timeLeft / 60)
        let seconds = timeLeft % 60
        if (seconds < 10) {
          seconds = `0${seconds}`
        }
        return `${minutes}:${seconds}`
      } else {
        return "0:00"
      }
    }, 
    circleDasharray () {
      return `${(this.timeFraction * 283).toFixed(0)}
      283`
    },
    timeFraction () {
      const rawTimeFraction = this.timeLeft /this.timeLimit
      // without below, timer display would be off by 1 second
      return rawTimeFraction - (1 / this.timeLimit) * (1 - rawTimeFraction)
    },
    colorCodes () {
      return {
        info: {
          color: "green"
        },
        warning: {
          color: "orange",
          threshold: this.warningThreshold
        },
        alert: {
          color: "red",
          threshold: this.alertThreshold
        }
      }
    },
    remainingPathColor () {
      const { info, warning, alert } = this.colorCodes
      if (this.timeLeft <= alert.threshold) {
        return alert.color
      } else if (this.timeLeft <= warning.threshold) {
        return warning.color
      } else {
        return info.color
      }
    },
    disableStart () {
      return this.gameActive || this.timeLeft === 0
    }
  }, 
  methods: {
    startGame () {
      this.$emit('startTimer')
    },
    resetGame () {
      this.$emit('resetGame')
    }
  }
}
</script>

<style lang="scss" scoped>
.alert {
  color: red;
  font-size: 18px;
}
.timer {
  position: relative;
  width: 100px;
  height: 100px;

  &_circle {
    fill: none;
    stroke: none;
  }

  &_path-elapsed {
    stroke-width: 7px;
    stroke: grey;
  }

  &_path-remaining {
    stroke-width: 7px;
    stroke-linecap: round;
    transform: rotate(90deg);
    transform-origin: center;
    transition: 1s linear all;
    fill-rule: nonzero;
    stroke: currentColor;

    &.green {
      color: rgb(65, 184, 131);
    }
    &.orange {
      color: orange;
    }
    &.red {
      color: red;
    }
  }

  &_svg {
    transform: scaleX(-1);
  }

  &_label {
    position: absolute;
    width: 100px;
    height: 100px;
    top: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 30px;
  }

  .timer-buttons {
    display: flex;
    justify-content: center;
  }
}
</style>
