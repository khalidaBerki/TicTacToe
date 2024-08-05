<template>
    <div class="container">
      <h2 v-if="winner" class="winner-text">Winner: {{ winner }} yeaay</h2>
      <h2 v-else class="winner-text">Player Move: {{ player }}</h2>
      <button @click="reset" class="btn btn-success mb-3 reset-button">Reset</button>
      <div v-for="x in 3" :key="x" class="row">
        <button v-for="y in 3" :key="y" class="square" @click="move(x-1, y-1)">
          <span :class="{ neonX: squares[x-1][y-1] === 'X', neonO: squares[x-1][y-1] === 'O' }">{{ squares[x-1][y-1] }}</span>
        </button>
      </div>
      <h2 class="mt-5 winner-text">History</h2>
      <div class="winner-text">
        X won {{ xWins }} times
      </div>
      <div class="winner-text">
        O won {{ oWins }} times
      </div>
    </div>
  </template>
  
  <script>
  import { ref, computed, onMounted, watch } from 'vue';
  
  const calculateWinner = squares => {
    const lines = [
      [0, 1, 2],
      [3, 4, 5],
      [6, 7, 8],
      [0, 3, 6],
      [1, 4, 7],
      [2, 5, 8],
      [0, 4, 8],
      [2, 4, 6],
    ];
    for (let i = 0; i < lines.length; i++) {
      const [a, b, c] = lines[i];
      if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
        return squares[a];
      }
    }
    return null;
  };
  
  export default {
    setup() {
      const player = ref('X');
      const squares = ref([
        ['', '', ''],
        ['', '', ''],
        ['', '', '']
      ]);
      const winner = computed(() => calculateWinner(squares.value.flat()));
  
      const xWins = ref(0);
      const oWins = ref(0);
  
      const move = (x, y) => {
        if (winner.value || squares.value[x][y]) return;
        squares.value[x][y] = player.value;
        player.value = player.value === 'X' ? 'O' : 'X';
      };
  
      const reset = () => {
        player.value = 'X';
        squares.value = [
          ['', '', ''],
          ['', '', ''],
          ['', '', '']
        ];
      };
  
      const history = ref([]);
  
      watch(winner, (current, previous) => {
        if (current && !previous) {
          history.value.push(current);
          if (current === 'X') {
            xWins.value++;
          } else if (current === 'O') {
            oWins.value++;
          }
          localStorage.setItem('history', JSON.stringify(history.value));
          localStorage.setItem('xWins', xWins.value);
          localStorage.setItem('oWins', oWins.value);
        }
      });
  
      onMounted(() => {
        history.value = JSON.parse(localStorage.getItem('history')) || [];
        xWins.value = parseInt(localStorage.getItem('xWins')) || 0;
        oWins.value = parseInt(localStorage.getItem('oWins')) || 0;
      });
  
      return { winner, player, squares, move, reset, history, xWins, oWins };
    }
  };
  </script>
  
  <style scoped>
  @import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap');
  
  body {
    background: linear-gradient(135deg, #7f00ff, #e100ff);
    font-family: 'Roboto', sans-serif;
    margin: 0;
    padding: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    overflow: hidden;
  }
  
  .container {
    background: linear-gradient(135deg, #7f00ff, #e100ff);
    color: #fff;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 0 15px rgba(0, 0, 0, 0.5);
    text-align: center;
  }
  
  .square {
    background: rgba(255, 255, 255, 0.1);
    border: 1px solid rgba(255, 255, 255, 0.2);
    float: left;
    font-size: 70px;
    font-weight: bold;
    line-height: 100px;
    height: 100px;
    margin-right: -1px;
    margin-top: -1px;
    padding: 0;
    text-align: center;
    width: 100px;
    color: #fff;
    cursor: pointer;
    transition: background 0.3s;
  }
  
  .square:hover {
    background: rgba(255, 255, 255, 0.2);
  }
  
  .neonX {
    color: #ff073a;
    text-shadow: 0 0 5px #ff073a, 0 0 10px #ff073a, 0 0 20px #ff073a, 0 0 40px #ff073a;
  }
  
  .neonO {
    color: #0affef;
    text-shadow: 0 0 5px #0affef, 0 0 10px #0affef, 0 0 20px #0affef, 0 0 40px #0affef;
  }
  
  .row {
    display: flex;
    justify-content: center;
  }
  
  .winner-text {
    font-family: 'Roboto', sans-serif;
    font-size: 20px;
  }
  
  .reset-button {
    font-family: 'Roboto', sans-serif;
    background: #ff073a;
    color: #fff;
    border: none;
    padding: 10px 20px;
    cursor: pointer;
    transition: background 0.3s;
  }
  
  .reset-button:hover {
    background: #e100ff;
  }
  
  h2 {
    margin-top: 20px;
  }
  </style>
  