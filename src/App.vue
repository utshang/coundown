<template>
  <div>
    <button :disabled="isButtonDisabled" @click="handleClick">
      {{ buttonText }}
    </button>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

const countdown = ref(0);
const timer = ref(null);

const isButtonDisabled = computed(() => countdown.value > 0);
const buttonText = computed(() => countdown.value > 0 ? `${countdown.value} 秒後可再次點擊` : '獲取驗證碼');

const handleClick = () => {
  startCountdown();
};

const startCountdown = () => {
  countdown.value = 30;
  saveCountdown();
  timer.value = setInterval(() => {
    countdown.value--;
    saveCountdown();
    if (countdown.value <= 0) {
      clearInterval(timer.value);
    }
  }, 1000);
};

const saveCountdown = () => {
  const expireTime = Date.now() + countdown.value * 1000;
  localStorage.setItem('countdownExpireTime', expireTime);
};

const loadCountdown = () => {
  const expireTime = localStorage.getItem('countdownExpireTime');
  if (expireTime) {
    const remainingTime = Math.ceil((expireTime - Date.now()) / 1000);
    if (remainingTime > 0) {
      countdown.value = remainingTime;
      timer.value = setInterval(() => {
        countdown.value--;
        if (countdown.value <= 0) {
          clearInterval(timer.value);
        }
      }, 1000);
    }
  }
};

onMounted(() => {
  loadCountdown();
});
</script>

<style scoped>
button {
  padding: 10px 20px;
  font-size: 16px;
}
</style>
