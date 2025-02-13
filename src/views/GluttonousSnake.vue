<template>
  <div id="snake">
    <h1>贪吃蛇</h1>
    <div class="game-board" @keydown="handleKeydown" tabindex="0" ref="board">
      <div v-for="(row, rowIndex) in board" :key="rowIndex" class="row">
        <div
            v-for="(cell, cellIndex) in row"
            :key="cellIndex"
            :class="['cell', getCellClass(rowIndex, cellIndex)]"
        ></div>
      </div>
    </div>
    <button @click="startGame">开始游戏</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      boardSize: 20,
      board: [],
      snake: [{x: 5, y: 5}],
      direction: 'right',
      food: {x: 10, y: 10},
      gameInterval: null,
    }
  },
  methods: {
    initBoard() {
      this.board = Array.from({length: this.boardSize}, () =>
          Array(this.boardSize).fill(null)
      );
    },
    getCellClass(row, col) {
      if (this.snake.some(segment => segment.x === col && segment.y === row)) {
        return 'snake';
      }
      if (this.food.x === col && this.food.y === row) {
        return 'food';
      }
      return '';
    },
    startGame() {
      this.initBoard();
      this.snake = [{x: 5, y: 5}];
      this.direction = 'right';
      this.placeFood();
      if (this.gameInterval) clearInterval(this.gameInterval);
      this.gameInterval = setInterval(this.moveSnake, 200);
      this.$refs.board.focus();
    },
    placeFood() {
      this.food = {
        x: Math.floor(Math.random() * this.boardSize),
        y: Math.floor(Math.random() * this.boardSize),
      };
    },
    moveSnake() {
      const head = {...this.snake[0]};
      switch (this.direction) {
        case 'up':
          head.y -= 1;
          break;
        case 'down':
          head.y += 1;
          break;
        case 'left':
          head.x -= 1;
          break;
        case 'right':
          head.x += 1;
          break;
      }

      if (this.checkCollision(head)) {
        alert('游戏结束');
        clearInterval(this.gameInterval);
        return;
      }

      this.snake.unshift(head);

      if (head.x === this.food.x && head.y === this.food.y) {
        this.placeFood();
      } else {
        this.snake.pop();
      }
    },
    checkCollision(head) {
      return (
          head.x < 0 ||
          head.x >= this.boardSize ||
          head.y < 0 ||
          head.y >= this.boardSize ||
          this.snake.some(segment => segment.x === head.x && segment.y === head.y)
      );
    },
    handleKeydown(event) {
      switch (event.key) {
        case 'ArrowUp':
          if (this.direction !== 'down') this.direction = 'up';
          break;
        case 'ArrowDown':
          if (this.direction !== 'up') this.direction = 'down';
          break;
        case 'ArrowLeft':
          if (this.direction !== 'right') this.direction = 'left';
          break;
        case 'ArrowRight':
          if (this.direction !== 'left') this.direction = 'right';
          break;
      }
    },
  },
  mounted() {
    this.startGame();
  },
}
</script>

<style>
#app {
  text-align: center;
}

.game-board {
  display: inline-block;
  border: 1px solid #000;
  outline: none;
}

.row {
  display: flex;
}

.cell {
  width: 20px;
  height: 20px;
  border: 1px solid #ddd;
}

.snake {
  background-color: green;
}

.food {
  background-color: red;
}
</style>
