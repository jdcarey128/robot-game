<template>
  <h2>Leader Board</h2>
  <div class="leader-board-scores">
    <table>
      <thead>
        <th>Name</th>
        <th>Score</th>
        <th>Date Set</th>
      </thead>
      <tbody>
        <tr v-for="(user, index) in state.playerLeaders" :key="index">
          <td>{{user.name}}</td>
          <td>{{user.score}}</td>
          <td>{{formatDate(user.created)}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import { reactive } from 'vue'
import userScores from '@/assets/userScores.js'

export default {
  name: 'LeaderBoard',
  setup () {
    const state = reactive({
      playerLeaders: userScores
    })

    function formatDate (date) {
      return new Date(date).toLocaleDateString()
    }

    return {
      state,
      formatDate
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
