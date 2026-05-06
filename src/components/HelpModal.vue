<script setup lang="ts">
defineProps<{ show: boolean }>()
const emit = defineEmits<{ close: [] }>()
</script>

<template>
  <Teleport to="body">
    <Transition name="modal">
      <div v-if="show" class="modal-overlay" @click.self="emit('close')">
        <div class="modal-content">
          <button class="modal-close" @click="emit('close')">
            &times;
          </button>
          <div class="modal-body">
            <p>出题人共有 <b>16</b> 次提示机会</p>

            <p>推荐布局：</p>
            <img style="border: 1px solid #666" src="/example.png">
          </div>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<style scoped>
.modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.45);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-content {
  width: min(600px, 92vw);
  max-height: 85vh;
  background: #fff;
  border-radius: 12px;
  position: relative;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
}

.modal-close {
  position: absolute;
  top: 12px;
  right: 12px;
  width: 32px;
  height: 32px;
  border-radius: 50%;
  border: none;
  background: #f0f0f0;
  font-size: 20px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #666;
  transition: background 0.2s;
  z-index: 10;
  line-height: 1;
}

.modal-close:hover {
  background: #ddd;
  color: #333;
}

.modal-body {
  padding: 36px 28px 28px;
  overflow-y: auto;
  font-family: "Noto Serif SC", "SimSun", serif;
  color: #333;
  line-height: 1.8;
}

.modal-body h2 {
  font-size: 22px;
  margin: 0 0 20px;
  text-align: center;
}

.modal-body h3 {
  font-size: 17px;
  margin: 24px 0 8px;
  color: #c0392b;
}

.modal-body p,
.modal-body li {
  font-size: 15px;
  margin: 4px 0;
}

.modal-body ul {
  padding-left: 20px;
}

.modal-body img {
  display: block;
  max-width: 100%;
  margin: 12px auto;
  border-radius: 8px;
}

/* ---- 弹窗动画 ---- */
.modal-enter-active {
  animation: modal-in 0.25s ease-out;
}
.modal-leave-active {
  animation: modal-out 0.2s ease-in;
}

@keyframes modal-in {
  from {
    opacity: 0;
    transform: scale(0.94);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

@keyframes modal-out {
  from {
    opacity: 1;
    transform: scale(1);
  }
  to {
    opacity: 0;
    transform: scale(0.94);
  }
}
</style>
