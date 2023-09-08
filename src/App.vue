<template>
  <div class='frisbee-draft'>
    <button @click='startDraft()' class='start-draft'>Start Draft</button>
    <input type="file" @change="handleCSVFileUpload" accept=".csv" class='file-browser'/>
    <div class='title mb'>Frisbee Draft</div>
    <button v-if='!draftStarted' @click='addTeam()'>Add Team</button>
    <div v-if='showTeamNameInput'>
      <input v-model="teamName" placeholder="Enter your team's name" @keyup.enter="submitTeamName()"/>
      <button @click='submitTeamName()'>Submit</button>
    </div>
    <div class='draft-grid'>
      <div class='team' :class='teamColor(team.index)' v-for='team in teams' :key='team.index'>{{team.name}}</div>
    </div>
    <div class='player-grid'>
      <div class='player' v-for='player in playerList' :key='player.index'>{{player.AdditionalComments}}</div>
    </div>
  </div>
</template>

<script>
import Papa from 'papaparse'

export default {
  name: 'App',
  data() {
    return {
      draftStarted: false,
      playerIndex: 0,
      playerList: [],
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
    handleCSVFileUpload(event) {
      const file = event.target.files[0];

      Papa.parse(file, {
        header: false,
        skipEmptyLines: true,
        complete: this.processCSVData
      })
    },
    processCSVData(results) {
      // Access the parsed data in results.data
      const csvData = results.data

      // Define custom headers or use column indexes
      const customHeaders = [
        'Timestamp',
        'PlayerName',
        'EmailAddress',
        'WeeksAvailable',
        'AdditionalComments',
        'PaymentMethod',
        'FitnessLevel',
        'Captain',
        'Gender',
        'SkillLevel'
      ]

      // Iterate through each row of data and create player objects
      const players = csvData.map(row => {
        const player = {}
        customHeaders.forEach((header, index) => {
           player[header] = row[index]
        })
        player['index'] = this.playerIndex
        this.playerIndex++
        return player
      })

      const filteredPlayers = players.filter(player => player['AdditionalComments'] !== '')

      this.playerList = filteredPlayers

      // Now you have an array of player objects to work with
      console.log(this.playerList)
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
  height: 30vh;
}

.player-grid {
  align-items: center;
  display: flex;
  flex-wrap: wrap;
}

.team {
  background-color: #ADD8E6;
  min-width: 100px;
  border-radius: 15px;
  padding-left: 10px;
  padding-right: 10px;
}

.player {
  background-color: #FFFFFF;
  min-width: 100px;
  border-radius: 15px;
  padding-left: 10px;
  padding-right: 10px;
  margin: 5px;
}

.file-browser {
  position: absolute;
  top: 20px;
  left: 20px;
}

.start-draft {
  position: absolute;
  top: 20px;
  right: 20px;
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

</style>
