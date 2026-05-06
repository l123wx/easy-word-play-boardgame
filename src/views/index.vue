<script setup lang="ts">
import { ref, computed, reactive } from 'vue'
import { gameData } from '../data/gameData'

const state = ref<'start' | 'playing'>('start')
const shownIndices = reactive(new Set<number>())
const currentIndex = ref(-1)

const currentChars = computed(() => currentIndex.value >= 0 ? gameData[currentIndex.value] : [])
const remaining = computed(() => gameData.length - shownIndices.size)

const gridPositions = ['tl', 'tr', 'bl', 'br']

function pickRandomUnseen() {
  const available = gameData.map((_, i) => i).filter(i => !shownIndices.has(i))
  if (available.length === 0) return undefined
  const idx = available[Math.floor(Math.random() * available.length)]
  shownIndices.add(idx)
  return idx
}

function startGame() {
  state.value = 'playing'
  const idx = pickRandomUnseen()
  if (idx !== undefined) currentIndex.value = idx
}

function pickNext() {
  const idx = pickRandomUnseen()
  if (idx !== undefined) currentIndex.value = idx
}
</script>

<template>
  <div class="page">
    <Transition name="window" mode="out-in">
      <div v-if="state === 'start'" key="start" class="game-screen">
        <p class="remaining-placeholder" />
        <div class="grid-container">
          <div v-for="(char, i) in ['组', '词', '成', '语']" :key="i" class="tianzige">
            <div class="cross" />
            <span class="char">{{ char }}</span>
            <span class="corner" :class="gridPositions[i]">{{ i + 1 }}</span>
          </div>
        </div>
        <button class="btn" @click="startGame">开始游戏</button>
      </div>

      <div v-else key="playing" class="game-screen">
        <p class="remaining">剩余：{{ remaining }}</p>

        <Transition name="grid" mode="out-in">
          <div class="grid-container" :key="currentIndex">
            <div v-for="(char, i) in currentChars" :key="i" class="tianzige">
              <div class="cross" />
              <span class="char">{{ char }}</span>
              <span class="corner" :class="gridPositions[i]">{{ i + 1 }}</span>
            </div>
          </div>
        </Transition>

        <div class="bottom-bar">
          <button v-if="remaining > 0" class="btn" @click="pickNext">下一题</button>
          <p v-else class="done">已完成</p>
        </div>
      </div>
    </Transition>
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #f5f0e8;
}

.btn {
  padding: 10px 32px;
  font-size: 16px;
  border: 2px solid #333;
  background: transparent;
  color: #333;
  border-radius: 0;
  cursor: pointer;
  transition: background 0.2s;
}

.btn:hover {
  background: #333;
  color: #f5f0e8;
}

/* ---- 游戏页 ---- */
.game-screen {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 24px;
  margin-top: -5vh;
}

.remaining,
.remaining-placeholder {
  margin: 0;
  font-size: 14px;
  color: #999;
  height: 20px;
  line-height: 20px;
}

.remaining-placeholder {
  visibility: hidden;
}

.grid-container {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: 1fr 1fr;
  gap: 40px;
}

.tianzige {
  position: relative;
  width: min(200px, 38vw);
  height: min(200px, 38vw);
  border: 3px solid #333;
  box-sizing: border-box;
  overflow: hidden;
}

@media (max-width: 480px) {
  .grid-container {
    gap: 16px;
  }
  .tianzige {
    border-width: 2px;
  }
}

.cross {
  position: absolute;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
}

.cross::before,
.cross::after {
  content: '';
  position: absolute;
  background: #999;
}

.cross::before {
  width: 1px;
  height: 100%;
  left: 50%;
  transform: translateX(-50%);
}

.cross::after {
  width: 100%;
  height: 1px;
  top: 50%;
  transform: translateY(-50%);
}

.corner {
  position: absolute;
  font-size: clamp(12px, 3vw, 16px);
  color: #999;
  user-select: none;
}

.corner.tl { top: 4px; left: 6px; }
.corner.tr { top: 4px; right: 6px; }
.corner.bl { bottom: 4px; left: 6px; }
.corner.br { bottom: 4px; right: 6px; }

.char {
  position: absolute;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: min(28vw, 140px);
  line-height: 1;
  font-family: "Noto Serif SC", "SimSun", serif;
  color: #333;
  pointer-events: none;
  transform: translateY(-4%);
}

.bottom-bar {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 16px;
}

.done {
  margin: 0;
  font-size: 14px;
  color: #999;
}

/* ---- Vue Transition 动画 ---- */
.window-enter-active {
  animation: window-in 0.35s ease-out;
}
.window-leave-active {
  animation: window-out 0.2s ease-in;
}

@keyframes window-in {
  from {
    opacity: 0;
    transform: perspective(500px) rotateX(6deg) scale(0.95);
  }
  to {
    opacity: 1;
    transform: perspective(500px) rotateX(0deg) scale(1);
  }
}

@keyframes window-out {
  from {
    opacity: 1;
    transform: perspective(500px) rotateX(0deg) scale(1);
  }
  to {
    opacity: 0;
    transform: perspective(500px) rotateX(-4deg) scale(0.95);
  }
}

.grid-enter-active {
  animation: grid-in 0.35s ease-out;
}
.grid-leave-active {
  animation: grid-out 0.15s ease-in;
}

@keyframes grid-in {
  from {
    opacity: 0;
    transform: perspective(500px) rotateX(8deg) scale(0.93);
  }
  to {
    opacity: 1;
    transform: perspective(500px) rotateX(0deg) scale(1);
  }
}

@keyframes grid-out {
  from {
    opacity: 1;
    transform: perspective(500px) rotateX(0deg) scale(1);
  }
  to {
    opacity: 0;
    transform: perspective(500px) rotateX(-5deg) scale(0.95);
  }
}
</style>
