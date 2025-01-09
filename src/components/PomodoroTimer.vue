<template>
  <div class="pomodoro-timer">
    <h1 class="timer-display">{{ minutes }}:{{ seconds < 10 ? '0' + seconds : seconds }}</h1>

    <div class="status">
      <h2>
        {{ isBreak
          ? "Sometimes we just need a mental vacation. Take a " + breakDuration + " minute break and start planning your next vacation."
          : "Challenges are what make life interesting and overcoming them is what makes life meaningful." }}
      </h2>
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

    <div class="button-group">
      <button @click="startTimer" :disabled="isRunning">Start</button>
      <button @click="pauseTimer" :disabled="!isRunning">Pause</button>
      <button @click="resetTimer">Reset</button>
    </div>
  </div>
  <footer class="footer">
    <p>This project is licensed under the GPL License. For more details, visit <a href="https://www.gnu.org/licenses/gpl-3.0.html" target="_blank">GPL License</a>.</p>
  </footer>
</template>

<script>
export default {
  data() {
    return {
      sessionDuration: 25,
      breakDuration: 5,
      totalTime: 25 * 60,
      minutes: 25,
      seconds: 0,
      timer: null,
      isRunning: false,
      isBreak: false,
    };
  },
  methods: {
    /**
     * Starts the timer if it is not already running.
     * Initializes a setInterval function to decrement the timer and update the display every second.
     * Switches to break or session mode when the timer reaches zero.
     */
    startTimer() {
      if (!this.isRunning) {
        this.isRunning = true;
        this.timer = setInterval(() => {
          if (this.totalTime > 0) {
            this.totalTime--;
            this.updateDisplay();
          } else {
            this.toggleMode();
          }
        }, 1000);
      }
    },

    /**
     * Pauses the timer by clearing the interval and setting the running state to false.
     */
    pauseTimer() {
      this.isRunning = false;
      clearInterval(this.timer);
    },

    /**
     * Resets the timer to the initial session duration and session mode.
     * Pauses the timer if it is running and resets the displayed time.
     */
    resetTimer() {
      this.pauseTimer();
      this.isBreak = false;
      this.totalTime = this.sessionDuration * 60;
      this.updateDisplay();
    },

    /**
     * Toggles between session and break modes.
     * Sets the total time based on the selected mode and updates the display.
     * Displays an alert indicating the current mode.
     */
    toggleMode() {
      this.isBreak = !this.isBreak;
      this.totalTime = this.isBreak ? this.breakDuration * 60 : this.sessionDuration * 60;
      this.updateDisplay();
      alert(this.isBreak ? "You deserve a break!" : "Time to work again!");
    },

    /**
     * Updates the displayed minutes and seconds based on the total time.
     */
    updateDisplay() {
      this.minutes = Math.floor(this.totalTime / 60);
      this.seconds = this.totalTime % 60;
    },
  },
  watch: {
    /**
     * Watches for changes in the session duration.
     * Updates the total time if the timer is not running.
     * @param {number} newDuration - The updated session duration in minutes.
     */
    sessionDuration(newDuration) {
      if (!this.isRunning) {
        this.totalTime = newDuration * 60;
        this.updateDisplay();
      }
    },
  },

  /**
   * Lifecycle hook that clears the timer interval when the component is destroyed.
   * Prevents memory leaks by stopping the interval.
   */
  beforeUnmount() {
    clearInterval(this.timer);
  },
};
</script>

<style scoped>
.pomodoro-timer {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
  padding: 20px;
  font-family: Arial, sans-serif;
  background-image: linear-gradient(rgba(0, 0, 0, 0.3), rgba(0, 0, 0, 0.3)), url("../assets/lofi_bgh.webp");
  background-size: cover;
  background-position: center;
}

.timer-display {
  font-size: 3.5rem;
  margin: 20px 0;
  color: #fff;
}

.status h2 {
  font-size: 1.3rem;
  margin: 20px;
  text-align: center;
  color: #fff;
}

.time-settings {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  margin: 20px 0;
}

.time-settings label {
  margin: 10px;
  font-size: 1rem;
  color: #fff;
}

.button-group {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 10px;
}

button {
  padding: 10px 15px;
  font-size: 1rem;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  background-color: #4267B2;
  color: white;
}

button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.footer {
  padding: 1px;
  background-color: #333;
  color: white;
  font-size: 0.9rem;
  text-align: center;
}

.footer a {
  color: #6384c7;
  text-decoration: none;
}

.footer a:hover {
  text-decoration: underline;
}

@media (max-width: 768px) {
  .timer-display {
    font-size: 2.5rem;
  }

  .status h2 {
    font-size: 1.1rem;
    margin: 15px;
  }

  .time-settings label {
    font-size: 0.9rem;
    margin: 5px;
  }

  button {
    font-size: 0.9rem;
    padding: 8px 12px;
  }

  .footer {
    font-size: 0.8rem;
  }
}

@media (max-width: 480px) {
  .timer-display {
    font-size: 2rem;
  }

  .status h2 {
    font-size: 1rem;
  }

  .time-settings {
    flex-direction: column;
    align-items: center;
  }

  .time-settings label {
    margin-bottom: 10px;
  }

  button {
    font-size: 0.8rem;
    padding: 6px 10px;
  }

  .footer {
    font-size: 0.7rem;
  }
}
</style>
