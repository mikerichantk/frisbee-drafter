<template>
  <div class='frisbee-draft'>
    <button @click='startDraft()' class='start-draft'>Start Draft</button>
    <div class='title mb'>Frisbee Draft</div>
    <button v-if='!draftStarted' @click='addTeam()'>Add Team</button>
    <div v-if='showTeamNameInput'>
      <input v-model="teamName" placeholder="Enter your team's name" @keyup.enter="submitTeamName()"/>
      <button @click='submitTeamName()'>Submit</button>
    </div>
    <div class='draft-grid'>
      <div class='team' :class='teamColor(team.index)' v-for='team in teams' :key='team.index'>{{team.name}}</div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      draftStarted: false,
      showTeamNameInput: false,
      teamIndex: 0,
      teamName: '',
      teams: [],
    }
  },
  methods:
  {
    addTeam()
    {
      this.showTeamNameInput = true
    },
    startDraft()
    {
      this.draftStarted = true
    },
    submitTeamName()
    {
      if (this.teamName)
      {
        const newTeam = {
          index: this.teamIndex,
          name: this.teamName,
        }
        this.teams.push(newTeam)
        this.teamIndex++
        this.showTeamNameInput = false
        this.teamName = ''
      }
    },
    teamColor(index)
    {
      const colors = ["bg-red", "bg-blue", "bg-green", "bg-purple", "bg-pink"]
      return colors[index%5]
    },
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}

.frisbee-draft {
  background-color: #fceed1;
  height: 100vh;
}

.draft-grid {
  padding-top: 20px;
  padding-left: 100px;
  padding-right: 100px;
  display: flex;
  justify-content: space-between;
}

.team {
  background-color: #ADD8E6;
  min-width: 100px;
  border-radius: 15px;
  padding-left: 10px;
  padding-right: 10px;
}

.bg-red {
  background-color: #E7A1A1;
}

.bg-blue {
  background-color: #C3FFEB;
}

.bg-green {
  background-color: #D1FF36;
}

.bg-purple {
  background-color: #D2D2FF;
}

.bg-pink {
  background-color: #F5D6F5;
}

.title {
  padding-top: 50px;
}

.mb {
  margin-bottom: 20px;
}

.start-draft {
  position: absolute;
  top: 20px;
  right: 20px;
}

</style>
