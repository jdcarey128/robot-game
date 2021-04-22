<template>
  <h2>Leader Board</h2>
  <div class="leader-board-scores">
    <table>
      <thead>
        <th>Name</th>
        <th>Score</th>
        <th>Date Set</th>
      </thead>
      <tbody v-if="!errored">
        <tr v-for="(user, index) in playerLeaders" :key="index">
          <td>{{user.name}}</td>
          <td>{{user.score}}</td>
          <td>{{formatDate(user.created_at)}}</td>
        </tr>
      </tbody>
      <tbody v-if="loading">
        <tr>
          <td colspan="3">...Loading...</td>
        </tr>
      </tbody>
      <tbody v-if="errored && !loading">
        <tr>
          <td colspan="3">The Leader Board cannot be accessed right now</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'LeaderBoard',
  emits: ['newHighScore'],
  props: {
    playerScore: {
      type: Number, 
      required: true
    },
    gameActive: {
      type: Boolean, 
      required: true
    }
  },
  data () {
    return {
      playerLeaders: null,
      leaderCountDisplay: 10,
      loading: true, 
      errored: false,
    }
  }, 
  created () {
    axios
      .get(`${process.env.VUE_APP_API_URL}/users`)
      .then(response => (this.playerLeaders = response.data))
      .catch(error => {
        alert("There was an error when retrieving the leader board.", error)
        this.errored = true
      })
      .finally(() => this.loading = false)
  },
  watch: {
    playerScore: function () {
      if (this.minimumCount || this.minimumScore) {
        this.$emit('newHighScore')
      }
    }
  },
  computed: {
    minimumScore () {
      return this.playerScore > this.minimumLeaderScore()
    },
    minimumCount () {
      return this.playerLeaders.length < this.leaderCountDisplay && this.playerScore > 0
    }
  },
  methods: {
    formatDate (date) {
      return new Date(date).toLocaleDateString()
    },
    minimumLeaderScore () {
      const leaders = this.playerLeaders
      return leaders[leaders.length - 1].score
    }
  }
}
</script>

<style lang="scss" scoped>
.leader-board-scores {
  display: flex;
  justify-content: center;
  padding-bottom: 50px;
}
table {
  border-collapse: collapse;
  width: 50%;
  border: 1px solid #ccc;
  padding: 10px;
}
th {
  @extend table;
  background-color: rgba(94, 219, 241, 0.2);
}

td {
  @extend table;
}

</style>
