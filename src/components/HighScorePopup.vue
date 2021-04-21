<template>
  <div class="popup">
    <div class="popup-inner">
      <slot />
      <p><strong>{{playerScore}}</strong></p>
      <input v-model="name" class="input-player-name" type="name" placeholder="Enter your name"><br>
      <button :disabled="!validSubmission" @click="submitHighScore" class="button-submit-score">
        Submit High Score
      </button>
    </div>
  </div>
</template>

<script>
// import axios from 'axios'

export default {
  name: "HighScorePopup",
  emits: ["highScoreSubmitted"],
  props: {
    playerScore: {
      type: Number, 
      required: true
    }
  },
  data () {
    return {
      name: null
    }
  },
  computed: {
    validSubmission () {
      return !!this.name
    }
  },
  methods: {
    submitHighScore () {
      this.$emit('highScoreSubmitted')
      // let url = ''
      // let contact = {
      //   "name": this.name, 
      //   "score": this.playerScore
      // }

      // return axios.post(url, contact)
    }
  }
}
</script>

<style lang="scss" scoped>
.popup {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0; 
  z-index: 99;
  background-color: rgba(0, 0, 0, 0.2);
  display: flex;
  align-items: center;
  justify-content: center;

  .popup-inner {
    background: #FFF;
    padding: 30px;
    border-radius: 5px;

    .input-player-name {
      padding: 10px;
    }

    .button-submit-score {
      margin-top: 20px;
    }
  }
}
</style>
