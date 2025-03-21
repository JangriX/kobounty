<script setup>
  import { ref } from 'vue'

  const players = ref([])
  const prizePool = ref(0)

  // Player add
  const addDialog = ref(false)
  const addInput = ref("")

  // Player KO
  const knockoutDialog = ref(false)
  const clickedPlayer = ref(null)

  //Game?
  const gameActive = ref(true)

  function toggleAddDialog(){
    addInput.value = ""
    addDialog.value = !addDialog.value
  }

  function toggleKnockoutDialog(playerIndex){
    clickedPlayer.value = playerIndex
    knockoutDialog.value = true
  }

  function addPlayer(){
    players.value.push({
      name: addInput.value,
      bounty: 15,
      winnings: 0,
      active: true,
      place: null
    })
    prizePool.value += 15
    toggleAddDialog()
  }

  function knockout(loserIndex, winnerIndex){
    const bounty = players.value[loserIndex].bounty
    players.value[winnerIndex].bounty += bounty/2
    players.value[winnerIndex].winnings += bounty/2
    players.value[loserIndex].bounty = 0
    players.value[loserIndex].active = false
    players.value[loserIndex].place = activePlayers() + 1
    knockoutDialog.value = false
    if (activePlayers() < 2){
      endGame()
    }
  }

  function rebuy(clickedPlayer){
    players.value[clickedPlayer].active = true
    players.value[clickedPlayer].bounty = 15
    players.value[clickedPlayer].place = null
    prizePool.value += 15
  }

  function activePlayers(){
    return players.value.filter((obj) => obj.active === true).length
  }

  function endGame(){
    gameActive.value = false
    let winner = players.value.filter((obj) => obj.active === true)[0]
    winner.winnings += winner.bounty
    if (players.value.count > 6){
      winner.winnings += prizePool.value*0.5
      players.value.filter((obj) => obj.place === 2)[0].winnings += prizePool.value*0.3
      players.value.filter((obj) => obj.place === 3)[0].winnings += prizePool.value*0.2
    } else {
      winner.winnings += prizePool.value*0.65
      players.value.filter((obj) => obj.place === 2)[0].winnings += prizePool.value*0.35
    }
  }
</script>

<template>
  <q-dialog v-model="addDialog">
    <q-card>
      <q-input v-model="addInput" label="Name" />
      <q-btn @click="addPlayer" label="Add" />
    </q-card>
  </q-dialog>
  <q-dialog v-model="knockoutDialog">
    <q-card style="min-width: 500px;">
      <p>Who knocked {{ players[clickedPlayer].name }} out?</p>
      <div class="column q-pa-md">
        <div class="row" v-for="(player, index) in players" :key="index">
          <div class="text-h4 col-10"> {{ player.name }}</div>
          <div class="text-h4 col-2">
            <q-btn @click="knockout(clickedPlayer, index)" v-if="index != clickedPlayer && player.active == true" icon="check" color="green" />
          </div>
        </div>
      </div>
    </q-card>
  </q-dialog>
  <q-page class="flex flex-center">
    <div class="full-height full-width position-relative">
      <q-btn @click="toggleAddDialog" label="Add Player" />
      <div class="row text-h2">
        <span>Money in pool: {{ prizePool }}</span>
      </div>
      <div class="column">
        <div class="row">
          <div class="text-h4 col-6 text-left text-deep-purple"> Name </div>
          <div class="text-h4 col-2 text-left text-green-9"> Bounty </div>
          <div class="text-h4 col-2 text-left text-green-9"> Winnings</div>
          <div class="text-h4 col-2 text-left"></div>
        </div>
        <div class="row" v-for="(player, index) in players" :key="index">
          <div class="text-h4 col-6 text-left text-deep-purple"> {{ player.name }}</div>
          <div class="text-h4 col-2 text-left text-green-9"> {{ player.bounty }}</div>
          <div class="text-h4 col-2 text-left text-green-9"> {{ player.winnings }}</div>
          <div class="text-h4 col-2 text-left" v-if="gameActive"> 
            <q-btn v-if="player.active" @click="toggleKnockoutDialog(index)" icon="close" color="red" />
            <q-btn v-else @click="rebuy(index)" icon="money" color="green" />
          </div>
        </div>
      </div>
    </div>
  </q-page>
</template>