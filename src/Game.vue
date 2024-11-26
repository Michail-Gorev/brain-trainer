<template>
    <div class="game-container">
      <div v-if="!gameStarted" class="start-screen">
        <h1>Тренажер мозга</h1>
        <p>Запомни порядок чисел и нажми на кружки в том же порядке!</p>
        <button @click="startGame">Начать игру</button>
      </div>

      <div v-else class="game-board">
        <div class="level-info">Уровень: {{ level }}</div>
        <div class="circle-grid">
          <div
            v-for="(circle, index) in circles"
            :key="index"
            :class="['circle', { 'empty': hideNumbers }]"
            :style="circle.style"
            @click="handleCircleClick(index)"
          >
            <span v-if="!hideNumbers">{{ circle.number }}</span>
          </div>
        </div>
      </div>
    </div>
  </template>

  <script>
  export default {
    data() {
      return {
        gameStarted: false,
        level: 1,
        score: 0,
        circles: [],
        sequence: [],
        userSequence: [],
        hideNumbers: false,
        maxLevels: 10,
      };
    },
    methods: {
      startGame() {
        this.gameStarted = true;
        this.startLevel();
      },
      startLevel() {
        this.generateSequence();
        this.hideNumbers = false;
        setTimeout(() => (this.hideNumbers = true), 5000);
      },
      generateSequence() {
        const numCircles = this.level + 1;
        const grid = [];
        const sequence = [];

        for (let i = 0; i < numCircles; i++) {
          const number = Math.floor(Math.random() * (999 - 1) + 1);
          const position = {
            top: `${Math.random() * 80 + 10}%`,
            left: `${Math.random() * 80 + 10}%`,
          };

          const circle = {
            number,
            style: { top: position.top, left: position.left },
          };

          grid.push(circle);
          sequence.push({ index: i, number });
        }

        this.circles = grid;
        this.sequence = sequence.sort((a, b) => a.number - b.number);
      },
      handleCircleClick(index) {
        if (this.hideNumbers) {
          this.userSequence.push(index);
          const currentStep = this.userSequence.length - 1;
          if (this.userSequence[currentStep] !== this.sequence[currentStep]?.index) {
            alert('Неправильно! Переход на следующий уровень.');
            this.nextLevel();
          } else if (this.userSequence.length === this.sequence.length) {
            alert('Правильно! Следующий уровень.');
            this.nextLevel(

            );
          }
        }
      },
      nextLevel() {
        if (this.level === this.maxLevels) {
          this.finishGame();
        } else {
          this.score += this.level
          this.level += 1;
          this.userSequence = [];
          this.startLevel();
        }
      },
      finishGame() {
        const score = this.calculateScore();
        this.$emit('finish', score);
      },
      calculateScore() {
        return Math.max(20, 90 - this.score * 10);
      },
    },
  };
  </script>

  <style>
  .game-container {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .start-screen {
    text-align: center;
  }
  .game-board {
    position: relative;
    width: 400px;
    height: 400px;
    background: #e0e7ff;
    border-radius: 15px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 10px;
  }
  .circle-grid {
    position: relative;
    width: 100%;
    height: 100%;
  }
  .circle {
    position: absolute;
    width: 50px;
    height: 50px;
    background: #4caf50;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 16px;
    color: white;
    font-weight: bold;
    cursor: pointer;
  }
  .circle.empty {
    background: transparent;
    border: 2px solid #4caf50;
    color: transparent;
  }
  </style>
