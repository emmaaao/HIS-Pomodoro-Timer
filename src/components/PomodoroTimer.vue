<template>
  <div class="pomodoro-timer">
    <h1>{{ minutes }}:{{ seconds < 10 ? '0' + seconds : seconds }}</h1>
    <button @click="startTimer" :disabled="isRunning">Start</button>
    <button @click="pauseTimer" :disabled="!isRunning">Pause</button>
    <button @click="resetTimer">Reset</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      totalTime: 25 * 60, // 25 minutes in seconds
      minutes: 25,
      seconds: 0,
      timer: null,
      isRunning: false,
    };
  },
  methods: {
    startTimer() {
      if (!this.isRunning) {
        this.isRunning = true;
        this.timer = setInterval(() => {
          if (this.totalTime > 0) {
            this.totalTime--;
            this.minutes = Math.floor(this.totalTime / 60);
            this.seconds = this.totalTime % 60;
          } else {
            this.stopTimer();
            alert('Pomodoro complete!');
          }
        }, 1000);
      }
    },
    pauseTimer() {
      this.isRunning = false;
      clearInterval(this.timer);
    },
    resetTimer() {
      this.pauseTimer();
      this.totalTime = 25 * 60;
      this.minutes = 25;
      this.seconds = 0;
    },
    stopTimer() {
      this.pauseTimer();
      this.totalTime = 25 * 60;
    }
  },
    beforeUnmount() {
    clearInterval(this.timer);
  }
};
</script>

<style scoped>
.pomodoro-timer {
  text-align: center;
  font-family: Arial, sans-serif;
}

h1 {
  font-size: 4rem;
  margin: 20px 0;
}

button {
  margin: 5px;
  padding: 10px 20px;
  font-size: 1.2rem;
  cursor: pointer;
}
</style>
