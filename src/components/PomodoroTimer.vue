<template>
  <div class="pomodoro-timer">
    <h1>{{ minutes }}:{{ seconds < 10 ? '0' + seconds : seconds }}</h1>

    <div class="status">
      <h2>{{ isBreak ? ' "Sometimes we just need a mental vacation. Take a 15-minute break and start planning your next vacation."' :
          '"Challenges are what make life interesting and overcoming them is what makes life meaningful."' }}</h2>
    </div>

    <div class="time-settings">
      <label>
        Sessie (min):
        <input type="number" v-model.number="sessionDuration" min="1"/>
      </label>
      <label>
        Pauze (min):
        <input type="number" v-model.number="breakDuration" min="1"/>
      </label>
    </div>

    <button @click="startTimer" :disabled="isRunning">Start</button>
    <button @click="pauseTimer" :disabled="!isRunning">Pause</button>
    <button @click="resetTimer">Reset</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // Session standard 25 min
      sessionDuration: 25,
      // Break standard 5 min
      breakDuration: 5,
      // In seconds
      totalTime: 25 * 60,
      minutes: 25,
      seconds: 0,
      timer: null,
      isRunning: false,
      // True during break
      isBreak: false,
    };
  },
  methods: {
    startTimer() {
      if (!this.isRunning) {
        this.isRunning = true;
        this.timer = setInterval(() => {
          if (this.totalTime > 0) {
            this.totalTime--;
            this.updateDisplay();
          } else {
            // switch to break or session
            this.toggleMode();
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
      // back to session mode
      this.isBreak = false;
      this.totalTime = this.sessionDuration * 60;
      this.updateDisplay();
    },
    toggleMode() {
      // switch mode
      this.isBreak = !this.isBreak;
      this.totalTime = this.isBreak
          ? this.breakDuration * 60
          : this.sessionDuration * 60;
      this.updateDisplay();
      alert(this.isBreak ? 'You deserve a break!' : 'Time to work again!');
    },
    updateDisplay() {
      this.minutes = Math.floor(this.totalTime / 60);
      this.seconds = this.totalTime % 60;
    },
  },
  watch: {
    // Watcher for changes in session or break
    sessionDuration(newDuration) {
      // Updates the time
      if (!this.isRunning) {
        this.totalTime = newDuration * 60;
        this.updateDisplay();
      }
    }
  },
  beforeUnmount() {
    clearInterval(this.timer);
  },
};
</script>

<style scoped>
.pomodoro-timer {
  text-align: center;
  height: 93vh;
  padding: 25px;
  font-family: Arial, sans-serif;
  background-image: linear-gradient(rgba(0, 0, 0, 0.3), rgba(0, 0, 0, 0.3)), url("../assets/lofi_bgh.webp");
  background-size: cover;
  background-position: center;
}

h1 {
  font-size: 4rem;
  margin-top: 150px;
  color: #ffffff;
}

h2 {
  font-size: 1.5rem;
  margin-bottom: 35px;
  color: #ffffff;
}

button {
  margin: 5px;
  padding: 10px 20px;
  font-size: 1.2rem;
  cursor: pointer;
}

.time-settings {
  margin-bottom: 20px;
}

label {
  margin-right: 15px;
  font-size: 1rem;
  color: #ffffff;
}
</style>