<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue';

const gridSize = 20;
const snake = ref([{ x: 10, y: 10 }]);
const food = ref({
  x: Math.floor(Math.random() * gridSize),
  y: Math.floor(Math.random() * gridSize),
});
const direction = ref('right');

const generateFood = () => {
  food.value = {
    x: Math.floor(Math.random() * gridSize),
    y: Math.floor(Math.random() * gridSize),
  };
};

const isSnakeCell = (x: number, y: number) => {
  return snake.value.some((part) => part.x === x && part.y === y);
};

const isFoodCell = (x: number, y: number) => {
  return food.value.x === x && food.value.y === y;
};

const moveSnake = () => {
  const head = { ...snake.value[0] };

  switch (direction.value) {
    case 'up':
      head.y--;
      break;
    case 'down':
      head.y++;
      break;
    case 'left':
      head.x--;
      break;
    case 'right':
      head.x++;
      break;
  }

  if (checkCollision(head)) {
    clearInterval(intervalId);

    // /**
    //  * Restart the game
    //  */
    // snake.value = [{ x: 10, y: 10 }];
    // intervalId = setInterval(moveSnake, 100);

    return;
  }

  snake.value.unshift(head);

  if (head.x === food.value.x && head.y === food.value.y) {
    generateFood();
  } else {
    snake.value.pop();
  }
};

const checkCollision = (head: { x: number; y: number }) => {
  return (
    head.x < 1 ||
    head.x > gridSize ||
    head.y < 1 ||
    head.y > gridSize ||
    isSnakeCell(head.x, head.y)
  );
};

const handleKeyDown = (event: KeyboardEvent) => {
  switch (event.key) {
    case 'ArrowUp':
      if (direction.value !== 'down') direction.value = 'up';
      break;
    case 'ArrowDown':
      if (direction.value !== 'up') direction.value = 'down';
      break;
    case 'ArrowLeft':
      if (direction.value !== 'right') direction.value = 'left';
      break;
    case 'ArrowRight':
      if (direction.value !== 'left') direction.value = 'right';
      break;
  }
};

let intervalId: ReturnType<typeof setInterval> | undefined;

onMounted(() => {
  document.addEventListener('keydown', handleKeyDown);
  intervalId = setInterval(moveSnake, 100);
});

onBeforeUnmount(() => {
  clearInterval(intervalId);
  document.removeEventListener('keydown', handleKeyDown);
});
</script>

<template>
  <div id="game">
    <div class="grid">
      <div v-for="row in gridSize" :key="row" class="row">
        <div
          v-for="col in gridSize"
          :key="col"
          class="cell"
          :class="{
            snakeDown: isSnakeCell(col, row) && direction === 'down',
            snakeRight: isSnakeCell(col, row) && direction === 'right',
            snakeUp: isSnakeCell(col, row) && direction === 'up',
            snakeLeft: isSnakeCell(col, row) && direction === 'left',
            food: isFoodCell(col, row),
          }"
        />
      </div>
    </div>
  </div>
</template>

<style scoped>
#game {
  background-color: #eee;
  border: 4px solid #555;
  display: flex;
  justify-content: center;
}

.grid {
  display: grid;
  grid-template-rows: repeat(20, 1fr);
  width: 360px;
  height: 360px;
}

.row {
  /* Key addition for row layout: */
  display: flex;
}

.cell {
  width: 16px;
  height: 16px;
  background-color: #ddd;
  border: 1px solid #ccc;
}

.food {
  background-color: green;
}

.snakeDown {
  background: url(character.png) 0px 0px;
}

.snakeRight {
  background: url(character.png) -16px 0px;
}

.snakeUp {
  background: url(character.png) -32px 0px;
}

.snakeLeft {
  background: url(character.png) -48px 0px;
}
</style>
