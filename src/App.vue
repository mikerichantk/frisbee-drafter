<template>
  <div class='frisbee-draft'>
    <button v-if="!draftStarted" @click='startDraft()' class='start-draft'>Start Draft</button>
    <input v-if="!draftStarted" type="file" @change="handleCSVFileUpload" accept=".csv" class='file-browser'/>
    <div class='title mb'>Frisbee Draft M({{ totalMale }}) F({{ totalFemale }})</div>
    <button v-if='!draftStarted' @click='addTeam()'>Add Team</button>
    <div v-if='showTeamNameInput'>
      <input v-model="teamName" placeholder="Enter your team's name" @keyup.enter="submitTeamName()"/>
      <button @click='submitTeamName()'>Submit</button>
    </div>
    <div class='draft-grid'>
      <div class="flex space-between">
        <div
          class='team'
          :class="{[teamColor(team.index)]: true, choose: clickedPlayer != null}"
          v-for='team in teams'
          :key='team.index'
          @click="handleTeamClick(team.index)"
        >
          {{team.name}} M({{ male(team.teamPlayers) }}) F({{ female(team.teamPlayers) }})
        </div>
      </div>
      <div class="flex space-between">
        <div
          class="player-columns"
          v-for="team in teams"
          :key="team.index"
        >
          <div
              class="team-players player"
              v-for="(player, index) in team.teamPlayers"
              :key="index"
            >
              {{ player['Your Name'] }}
          </div>
        </div>
      </div>
    </div>
    <div class='player-grid'>
      <div
        class='player'
        :class="{clicked: clickedPlayer === index}"
        v-for='(player, index) in playerList'
        :key='player.index'
        @click="handlePlayerClick(index)"
      >
          {{ player['Your Name'] }}
      </div>
    </div>
    <div v-if="clickedPlayer != null">
      <div
        class="left-align padding-left padding-top"
        v-for="(data, field, index) in playerList[clickedPlayer]"
        :key="index"
      >
       {{ field }}: {{ data }}
      </div>
    </div>
  </div>
</template>

<script>
import Papa from 'papaparse'

export default {
  name: 'App',
  data() {
    return {
      clickedPlayer: null,
      draftStarted: false,
      playerList: [],
      showTeamNameInput: false,
      teamIndex: 0,
      teamName: '',
      teams: [],
    }
  },
  computed:
  {
    totalFemale()
    {
      return this.playerList.filter(player => player['Gender'] == 'Female').length
    },
    totalMale()
    {
      return this.playerList.filter(player => player['Gender'] == 'Male').length
    },
  },
  methods:
  {
    addTeam()
    {
      this.showTeamNameInput = true
    },
    female(players) {
      return players.filter(player => player['Gender'] == 'Female').length
    },
    handleCSVFileUpload(event) {
      const file = event.target.files[0];

      Papa.parse(file, {
        header: false,
        skipEmptyLines: true,
        complete: this.processCSVData
      })
    },
    handleTeamClick(index) {
      if (this.clickedPlayer == null) { return }
      this.teams[index].teamPlayers.push(this.playerList[this.clickedPlayer])
      this.playerList.splice(this.clickedPlayer, 1)
      this.clickedPlayer = null
    },
    handlePlayerClick(index) {
      if (this.clickedPlayer === index)
      {
        this.clickedPlayer = null
      }
      else
      {
        this.clickedPlayer = index
      }
    },
    male(players) {
      return players.filter(player => player['Gender'] == 'Male').length
    },
    processCSVData(results) {
      // Access the parsed data in results.data
      const csvData = results.data

      // Define custom headers or use column indexes
      const customHeaders = csvData[0]

      // Iterate through each row of data and create player objects
      const players = csvData.map(row => {
        const player = {}
        customHeaders.forEach((header, index) => {
          player[header] = row[index]
        })
        return player
      })

      const filteredPlayers = players.filter(player => (player['Your Name'] !== ''))

      filteredPlayers.shift()

      filteredPlayers.sort((a, b) => 
        {
          return a['Your Name'].localeCompare(b['Your Name'])
        })

      this.playerList = filteredPlayers
      
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
          teamPlayers: [],
        }
        this.teams.push(newTeam)
        this.teamIndex++
        this.showTeamNameInput = false
        this.teamName = ''
      }
    },
    teamColor(index)
    {
      const colors = ["bg-salmon", "bg-blue", "bg-green", "bg-yellow"]
      return colors[index%4]
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
  color: white;
}

.frisbee-draft {
  background-color: darkslateblue;
  height: 300vh;
}

.flex {
  display: flex;
}

.space-between {
  justify-content: space-between;
}

.player-columns {
  display: flex;
  flex-direction: column;
}

.draft-grid {
  padding-top: 20px;
  padding-left: 100px;
  padding-right: 100px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  margin-bottom: 100px;
}

.player-grid {
  align-items: center;
  justify-content: center;
  display: flex;
  flex-wrap: wrap;
}

.team {
  display: flex;
  justify-content: center;
  align-items: center;
  font-weight: 600;
  color: black;
  min-width: 100px;
  height: 50px;
  border-radius: 15px;
  padding-left: 10px;
  padding-right: 10px;
}

.choose {
  cursor: pointer;
  box-shadow: 0 0 5px 5px gold;
}

.player {
  color: black;
  background-color: #FFFFFF;
  min-width: 100px;
  border-radius: 15px;
  padding-left: 10px;
  padding-right: 10px;
  margin: 5px;
  cursor: pointer;
}

.player:hover {
  background-color: lightgray;
}

.clicked {
  background-color: pink !important;
}

.left-align {
  text-align: left;
}

.padding-left {
  padding-left: 20px;
}

.padding-top {
  padding-top: 20px;
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

.bg-salmon {
  background-color: lightsalmon;
}

.bg-blue {
  background-color: lightblue;
}

.bg-green {
  background-color: lightgreen;
}

.bg-yellow {
  background-color: lightyellow;
}

.title {
  padding-top: 50px;
}

.mb {
  margin-bottom: 20px;
}

</style>
