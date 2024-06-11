<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue';

const gridSize = 20;
const snake = ref({ x: 10, y: 10 });

const movement = ref<'down' | 'right' | 'left' | 'up' | 'stopped'>('stopped');

const getClass = (x: number, y: number) => {
  if (snake.value.x === x && snake.value.y === y) {
    return movement.value === 'stopped'
      ? 'snake'
      : 'snake snake-' + movement.value;
  } else {
    return '';
  }
};

let clearMovementId: ReturnType<typeof setTimeout> | undefined = undefined;

const moveSnake = () => {
  switch (movement.value) {
    case 'up':
      snake.value.y > 1 && snake.value.y--;
      break;
    case 'down':
      snake.value.y < gridSize && snake.value.y++;
      break;
    case 'left':
      snake.value.x > 1 && snake.value.x--;
      break;
    case 'right':
      snake.value.x < gridSize && snake.value.x++;
      break;
  }
};

const handleKeyDown = (event: KeyboardEvent) => {
  if (clearMovementId) {
    clearTimeout(clearMovementId);
    clearMovementId = undefined;
  }

  switch (event.key) {
    case 'ArrowUp':
      movement.value = 'up';
      break;
    case 'ArrowDown':
      movement.value = 'down';
      break;
    case 'ArrowLeft':
      movement.value = 'left';
      break;
    case 'ArrowRight':
      movement.value = 'right';
      break;
  }

  moveSnake();
};

const handleKeyUp = () => {
  clearMovementId && clearTimeout(clearMovementId);
  clearMovementId = setTimeout(() => {
    clearMovementId = undefined;
    movement.value = 'stopped';
  }, 500);
};

onMounted(() => {
  document.addEventListener('keydown', handleKeyDown);
  document.addEventListener('keyup', handleKeyUp);
});

onBeforeUnmount(() => {
  document.removeEventListener('keydown', handleKeyDown);
  document.removeEventListener('keyup', handleKeyUp);
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
          :class="getClass(col, row)"
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
  display: flex;
}

.cell {
  width: 16px;
  height: 16px;
  background-color: #ddd;
  border: 1px solid #ccc;
}

.snake {
  background: url(character.png) 0px 0px;
}

@keyframes snake-down {
  from {
    background-position: 0px -16px;
  }
  to {
    background-position: -64px -16px;
  }
}

@keyframes snake-left {
  from {
    background-position: 0px -32px;
  }
  to {
    background-position: -64px -32px;
  }
}

@keyframes snake-up {
  from {
    background-position: 0px -48px;
  }
  to {
    background-position: -64px -48px;
  }
}

@keyframes snake-right {
  from {
    background-position: 0px -64px;
  }
  to {
    background-position: -64px -64px;
  }
}

.snake-down {
  animation: snake-down 500ms steps(4) infinite;
}

.snake-left {
  animation: snake-left 500ms steps(4) infinite;
}

.snake-up {
  animation: snake-up 500ms steps(4) infinite;
}

.snake-right {
  animation: snake-right 500ms steps(4) infinite;
}
</style>
