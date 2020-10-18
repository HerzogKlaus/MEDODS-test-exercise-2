<template>
  <div id="wrapper">
    <h1 v-show="!mode">Выбери уровень сложности и нажми ИГРАТЬ</h1>
    <div :class="{ shake: isWrong, grow: isWon }">
      <div class="game" :class="{ fullSize: playing }">
        <div
          class="square"
          @click="clicked(1)"
          :class="{ lit: isLit[1] }"
        ></div>
        <div
          class="square"
          @click="clicked(2)"
          :class="{ lit: isLit[2] }"
        ></div>
        <div
          class="square"
          @click="clicked(3)"
          :class="{ lit: isLit[3] }"
        ></div>
        <div
          class="square"
          @click="clicked(4)"
          :class="{ lit: isLit[4] }"
        ></div>
        <div class="start" @click="startGame" v-show="mode">
          {{ centerButton }}
          <span v-show="playing">{{ showScore }}</span>
        </div>
      </div>
    </div>
    <div class="bottom-bar">
      <p>Сложность:</p>
      <input type="radio" name="mode" value="easy" v-model="mode"/> Легкая
      <input type="radio" name="mode" value="medium" v-model="mode"/> Средняя
      <input type="radio" name="mode" value="hard" v-model="mode"/> Сложная
    </div>
  </div>
</template>

<script>
export default {
  data: () => ({
    centerButton: "ИГРАТЬ",
    playing: false,
    isClickable: false,
    isWon: false,
    isWrong: false,
    mode: '',
    score: 0,
    sequence: [],
    sequenceInterval: '',
    playerSequence: [],
    sounds: [
      new Audio("https://s3.amazonaws.com/freecodecamp/simonSound1.mp3"),
      new Audio("https://s3.amazonaws.com/freecodecamp/simonSound2.mp3"),
      new Audio("https://s3.amazonaws.com/freecodecamp/simonSound3.mp3"),
      new Audio("https://s3.amazonaws.com/freecodecamp/simonSound4.mp3"),
    ],
    isLit: {
      1: false,
      2: false,
      3: false,
      4: false,
    },
  }),

  computed: {
    showScore() {
      if (this.isWon) {
        return "Сыграть снова?";
      }
      return "Рекорд: " + this.score;
    },
  },
  watch: {
    strict() {
      this.startGame();
    },
  },
  methods: {
    startGame() {
      this.playing = true;
      this.sequence = [];
      this.playerSequence = [];
      this.centerButton = "СБРОС";
      this.isWon = false;
      this.isWrong = false;
      this.score = 0;
      this.showSequence();
    },
    clicked(tile) {
      if (this.isClickable) {
        this.playSound(tile);
        this.lightUp(tile);
        this.playerSequence.push(tile);
        this.checkWinLose();
      }
    },
    checkWinLose() {
      for (let i = 0; i < this.playerSequence.length; i++) {
        if (this.playerSequence[i] !== this.sequence[i]) {
          this.playerSequence = [];
          this.centerButton = "Ошибка!";
          this.isWrong = true;
          setTimeout(() => {
            this.centerButton = "СБРОС";
            this.isWrong = false;
            this.score = 0;
          }, 1000);
          this.sequence = [];
        }
      }
      if (this.playerSequence.length === this.sequence.length) {
        this.playerSequence = [];
        this.score++;
        // if win condition, show win, dont continue.
        if (this.score === 20) {
          this.centerButton = "Победа!";
          this.isClickable = false;
          this.isWon = true;
        } else {
          this.showSequence();
        }
      }
    },
    lightUp(tile) {
      this.isLit[tile] = true;
      setTimeout(() => {
        this.isLit[tile] = false;
      }, 300);
    },
    playSound(tile) {
      this.sounds[tile - 1].play();
    },
    showSequence(redo) {
      let currentIndex = 0;
      if(this.mode === 'easy') {
        this.sequenceInterval = 1500
      } else if (this.mode === 'medium') {
        this.sequenceInterval = 1000
      } else if (this.mode === 'hard') {
        this.sequenceInterval = 400
      }
      this.isClickable = false;
      if (!redo) {
        // dont add number on incorrect answers
        this.sequence.push(Math.floor(Math.random() * 4 + 1));
      }
      this.sequenceInterval = setInterval(() => {
        if (currentIndex >= this.sequence.length) {
          clearInterval(this.sequenceInterval);
          return (this.isClickable = true);
        }
        this.playSound(this.sequence[currentIndex]);
        this.lightUp(this.sequence[currentIndex]);
        currentIndex++;
      }, this.sequenceInterval);
    },
  },
};
</script>