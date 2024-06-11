<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue';
import throttle from 'lodash.throttle';

const gridSize = 20;
const peake = ref({ x: 10, y: 10 });

const movement = ref<'down' | 'right' | 'left' | 'up' | 'stopped'>('stopped');

let clearMovementId: ReturnType<typeof setTimeout> | undefined = undefined;

const handleKeyDown = throttle(
  (event: KeyboardEvent) => {
    if (clearMovementId) {
      clearTimeout(clearMovementId);
      clearMovementId = undefined;
    }

    switch (event.key) {
      case 'ArrowUp':
        movement.value = 'up';
        peake.value.y > 1 && peake.value.y--;
        break;
      case 'ArrowDown':
        movement.value = 'down';
        peake.value.y < gridSize && peake.value.y++;
        break;
      case 'ArrowLeft':
        movement.value = 'left';
        peake.value.x > 1 && peake.value.x--;
        break;
      case 'ArrowRight':
        movement.value = 'right';
        peake.value.x < gridSize && peake.value.x++;
        break;
    }
  },
  125,
  { leading: true, trailing: true }
);

const handleKeyUp = () => {
  handleKeyDown.flush();
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
        <div v-for="col in gridSize" :key="col" class="cell" />
      </div>
      <div
        class="peake"
        :class="movement != 'stopped' ? 'peake-' + movement : ''"
        :style="{
          top: (peake.y - 1) * 16 + 'px',
          left: (peake.x - 1) * 16 + 'px',
        }"
      />
    </div>
  </div>
</template>

<style scoped>
#game {
  background-color: #eee;
  border: 4px solid #555;
  display: flex;
  justify-content: center;
  position: relative;
}

.grid {
  display: grid;
  grid-template-rows: repeat(20, 1fr);
  width: 320px;
  height: 320px;
}

.row {
  display: flex;
}

.cell {
  width: 14px;
  height: 14px;
  background-color: #ddd;
  border: 1px solid #ccc;
}

.peake {
  position: absolute;
  background: url(character.png) 0px 0px;
  width: 16px;
  height: 16px;
}

@keyframes peake-down {
  from {
    background-position: 0px -16px;
  }
  to {
    background-position: -64px -16px;
  }
}

@keyframes peake-left {
  from {
    background-position: 0px -32px;
  }
  to {
    background-position: -64px -32px;
  }
}

@keyframes peake-up {
  from {
    background-position: 0px -48px;
  }
  to {
    background-position: -64px -48px;
  }
}

@keyframes peake-right {
  from {
    background-position: 0px -64px;
  }
  to {
    background-position: -64px -64px;
  }
}

.peake-down {
  animation: peake-down 500ms steps(4) infinite;
}

.peake-left {
  animation: peake-left 500ms steps(4) infinite;
}

.peake-up {
  animation: peake-up 500ms steps(4) infinite;
}

.peake-right {
  animation: peake-right 500ms steps(4) infinite;
}
</style>
