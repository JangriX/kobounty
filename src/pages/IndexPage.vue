<script setup>
  import { ref } from 'vue'

  const players = ref([])
  const prizePool = ref(0)

  // Player add
  const addDialog = ref(false)
  const addInput = ref("")

  function toggleAddDialog(){
    addInput.value = ""
    addDialog.value = !addDialog.value
  }

  function addPlayer(){
    players.value.push({
      name: addInput.value,
      bounty: 15,
      winnings: 0
    })
    prizePool.value += 15
    toggleAddDialog()
  }
</script>

<template>
  <q-dialog v-model="addDialog">
    <q-card>
      <q-input v-model="addInput" label="Name" />
      <q-btn @click="addPlayer" label="Add" />
    </q-card>
  </q-dialog>
  <q-page class="flex flex-center">
    <q-btn @click="toggleAddDialog" label="Add Player" />
  </q-page>
</template>