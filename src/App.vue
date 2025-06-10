<script setup lang="ts">
import {ref, computed} from "vue";

const board = ref<string[]>(['', '', '', '', '', '', '', '', ''])
const currentPlayer = ref<'playerOne' | 'playerTwo'>('playerOne');

interface Player {
  colour: string;
  display: string
}

const players: Record<'playerOne' | 'playerTwo', Player> = {
  playerOne: {
    colour: 'background-color: #415e83;',
    display: 'Player 1'
  },
  playerTwo: {
    colour: 'background-color: #7aa5dc;',
    display: 'Player 2'
  }
}

const winner = computed((): 'playerOne' | 'playerTwo' | 'draw' | null => {
  const winPatterns = [
    [0, 1, 2], [3, 4, 5], [6, 7, 8],
    [0, 3, 6], [1, 4, 7], [2, 5, 8],
    [0, 4, 8], [2, 4, 6]
  ];

  for (const pattern of winPatterns) {
    const [a, b, c] = pattern;
    if (board.value[a] && board.value[a] === board.value[b] && board.value[a] === board.value[c]) {
      return board.value[a] as 'playerOne' | 'playerTwo';
    }
  }
  if (board.value.every(tile => tile !== '')) {
    return 'draw';
  }
  return null;
})

function makeMove(i: number): void {
  if (board.value[i] === '' && !winner.value) {
    board.value[i] = currentPlayer.value;
    currentPlayer.value = currentPlayer.value === 'playerOne' ? 'playerTwo' : 'playerOne';
  }
}

function resetGame(): void {
  board.value = ['', '', '', '', '', '', '', '', ''];
  currentPlayer.value = 'playerOne';
}

</script>

<template>
  <section class="text-section">
    <h4>Click on the tiles to play.</h4>
    <div v-if="!winner">
      <h5 class="current-player"
          :style="players[currentPlayer].colour">Current player: {{ players[currentPlayer].display }}</h5>
    </div>
    <div v-else-if="winner === 'draw'">
      <h5>It's a draw!</h5>
    </div>
    <div v-else>
    <span :style="players[winner].colour">
      {{ players[winner].display }} has won!
    </span>
    </div>
  </section>
  <section class="board">
    <div class="grid">
      <div class="tile"
           v-for="i in [0, 1, 2, 3, 4, 5, 6, 7, 8]"
           @click="makeMove(i)"
           :style="board[i] ? players[board[i] as 'playerOne' | 'playerTwo'].colour : ''">
        {{ i + 1 }}
      </div>
    </div>
  </section>
  <section class="button-section">
    <button @click="resetGame">Reset game</button>
  </section>
</template>

<style scoped>
* {
  font-family: 'Nunito', sans-serif;
}

.current-player {
  padding: 4px 8px;
  border-radius: 4px;
  color: #ffffff;
}

.text-section,
.board {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  border: 2px solid black;
}

.tile {
  padding: 50px;
  display: flex;
  justify-content: center;
  align-items: center;
  border-right: 2px solid black;
  border-bottom: 2px solid black;
}

.tile:nth-child(3n) {
  border-right: none;
}

.tile:nth-last-child(-n+3) {
  border-bottom: none;
}

.button-section {
  display: flex;
  justify-content: center;
  padding: 50px;
}

button {
  border-radius: 4px;
  font-size: 16px;
  cursor: pointer;
  background-color: #353232;
  color: whitesmoke;
}

button:hover {
  background-color: white;
  color: black;
}
</style>
