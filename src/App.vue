<script setup lang="ts">
import {ref, computed} from "vue";

const board = ref<Tile[]>([{}, {}, {}, {}, {}, {}, {}, {}, {}])
const currentPlayer = ref<'playerOne' | 'playerTwo'>('playerOne');
const winner = ref<Player | 'draw' | null>(null);
const winPattern = ref<number[]>([])

interface Player {
  style: string;
  displayName: string
}

interface Tile {
  colour?: string | undefined;
  player?: Player | undefined;
}

const players: Record<'playerOne' | 'playerTwo', Player> = {
  playerOne: {
    style: 'background-color: #415e83;',
    displayName: 'Player 1'
  },
  playerTwo: {
    style: 'background-color: #7aa5dc;',
    displayName: 'Player 2'
  }
}

function handleWinner(): Player | 'draw' | null {
  const winPatterns = [
    [0, 1, 2], [3, 4, 5], [6, 7, 8], // horizonal
    [0, 3, 6], [1, 4, 7], [2, 5, 8], // vertical
    [0, 4, 8], [2, 4, 6] // diagonal
  ];
  for (const pattern of winPatterns) { // winner check
    const [a, b, c] = pattern;
    if (board.value[a].player && board.value[a].player === board.value[b].player && board.value[a].player === board.value[c].player) {
      const winner = board.value[a].player;
      winPattern.value = pattern;

      console.log('winner is' + winner);

      return winner;
    }
  }
  if (board.value.every(tile => tile.player !== undefined)) { //checking that the game hasn't ended yet!
    console.log('draw');
    return 'draw';
  }
  return null;
}

function makeMove(i: number): void {
  if (winner.value) {
    return;
  }
  if (board.value[i].player) {
    return;
  }

  board.value[i].player = players[currentPlayer.value];
  board.value[i].colour = players[currentPlayer.value].style
  currentPlayer.value = currentPlayer.value === 'playerOne' ? 'playerTwo' : 'playerOne';

  winner.value = handleWinner();
}

function changeWinTileColour(): void {

}

function resetGame(): void {
  board.value = [{}, {}, {}, {}, {}, {}, {}, {}, {}]
  currentPlayer.value = 'playerOne';
  winner.value = null;
}

</script>

<template>
  <div class="app-wrapper">
    <div class="game-container">
      <section class="text-section">
        <h4>Click on the tiles to play.</h4>
        <div v-if="!winner">
          <h5 class="current-player"
              :style="players[currentPlayer].style">Current player: {{ players[currentPlayer].displayName }}</h5>
        </div>
        <div v-else-if="winner === 'draw'">
          <h5>It's a draw!</h5>
        </div>
        <div v-else>
          <span id="winner-text" :style="winner.style + 'color: whitesmoke;'">
            {{ winner.displayName }} has won!
          </span>
        </div>
      </section>
      <section class="board">
        <div class="grid">
          <div class="tile"
               v-for="i in [0, 1, 2, 3, 4, 5, 6, 7, 8]"
               @click="makeMove(i)"
               :style="board[i].colour">
            {{ i + 1 }}
          </div>
        </div>
      </section>
      <section class="button-section">
        <button @click="resetGame">Reset game</button>
      </section>
    </div>
  </div>
</template>

<style scoped>
* {
  font-family: 'Nunito', sans-serif;
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

.app-wrapper {
  background-color: #f0f2f5;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}

.game-container {
  background-color: #ffffff;
  border-radius: 12px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;
  gap: 25px;
  max-width: 450px;
  width: 90%;
  padding: 30px;
}

.current-player {
  padding: 4px 8px;
  border-radius: 4px;
  color: whitesmoke;
}

.text-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 15px;
  text-align: center;
}

#winner-text {
  padding: 6px 12px;
  border-radius: 6px;
  color: whitesmoke;
  font-weight: bold;
}

.board {
  display: flex;
  justify-content: center;
  align-items: center;
}

.grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  border: 4px solid #333;
  border-radius: 8px;
  overflow: hidden;
  width: 100%;
  max-width: 360px;
  aspect-ratio: 1 / 1;
}

.tile {
  font-weight: bold;
  width: 100%;
  min-height: 120px;
  display: flex;
  justify-content: center;
  align-items: center;
  border-right: 2px solid #555;
  border-bottom: 2px solid #555;
  cursor: pointer;
  user-select: none;
  transition: background-color 0.3s ease, transform 0.1s ease;
}

/* to fix superposed borders */

.tile:nth-child(3n) {
  border-right: none;
}

.tile:nth-last-child(-n+3) {
  border-bottom: none;
}

.tile:hover {
  background-color: rgba(0, 0, 0, 0.1);
}

.button-section {
  display: flex;
  justify-content: center;
  padding-top: 10px;
}

button {
  padding: 12px 25px;
  border-radius: 8px;
  font-size: 1.1em;
  font-weight: bold;
  cursor: pointer;
  background-color: #4CAF50;
  color: white;
  border: none;
  transition: background-color 0.3s ease, transform 0.1s ease, box-shadow 0.3s ease;
}

button:hover {
  background-color: #45a049;
  transform: translateY(-2px);
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.3);
}

button:active {
  transform: translateY(0);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}
</style>
