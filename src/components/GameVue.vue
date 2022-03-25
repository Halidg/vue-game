<template>
  <div class="game">
    <h1>Simon Game</h1>
    <div class="game__content">
      <div class="game__item game__item_1" @click="clicked(1)" :class="{'active': isActive[1]}"></div>
      <div class="game__item game__item_2" @click="clicked(2)" :class="{'active': isActive[2]}"></div>
      <div class="game__item game__item_3" @click="clicked(3)" :class="{'active': isActive[3]}"></div>
      <div class="game__item game__item_4" @click="clicked(4)" :class="{'active': isActive[4]}"></div>
    </div>
    <div class="game__start-btn" @click="startGame">{{ centerButton }}
        <span v-show="playing">{{ showScore }}</span>
    </div>
    <div class="game__mode">
      <p>Mode</p>
      <button :class="{active: activeBtn === 'btn1'}" @click="gameMode = 'easy'; activeBtn = 'btn1'">Easy</button>
      <button :class="{active: activeBtn === 'btn2'}" @click="gameMode = 'normal'; activeBtn = 'btn2'">Normal</button>
      <button :class="{active: activeBtn === 'btn3'}" @click="gameMode = 'hard'; activeBtn = 'btn3'">Hard</button>
  </div>
  </div>
</template>

<script>
export default {
  name: 'SimonGame',
  data() {
    return {
      centerButton: "START",
      playing: false,
      isClickable: false,
      isWon: false,
      isWrong: false,
      gameMode: 'normal',
      activeBtn: 'btn2',
      round: 0,
      sequence: [],
      sequenceInterval: null,
      playerSequence: [],
      sounds: {
        1: "https://s3.amazonaws.com/freecodecamp/simonSound1.mp3",
        2: "https://s3.amazonaws.com/freecodecamp/simonSound2.mp3",
        3: "https://s3.amazonaws.com/freecodecamp/simonSound3.mp3",
        4: "https://s3.amazonaws.com/freecodecamp/simonSound4.mp3",
        5: "http://www.freesound.org/data/previews/331/331912_3248244-lq.mp3"
      },
      isActive: {
        1: false,
        2: false,
        3: false,
        4: false
      }
    }
  },
  computed: {
    showScore() {
      if (this.isWon) {
        return "Play Again?"
      }
      return "Round: " + this.round
    }
  },
  methods: {
    playSound(tile) {
      if(this.sounds[tile]) {
        const audio = new Audio(this.sounds[tile])
        audio.play()
      }
    },
    startGame() {
      this.playing = true
      this.sequence = []
      this.playerSequence = []
      this.centerButton = "RESET"
      this.isWon = false
      this.isWrong = false
      this.round = 0
      clearInterval(this.sequenceInterval)
      this.showSequence()
    },
    clicked(tile) {
      if (this.isClickable) {
        this.playSound(tile)
        this.showActive(tile)
        this.playerSequence.push(tile)
        this.checkWinLose()
      }
    },
    checkWinLose() {
      for (let i = 0; i < this.playerSequence.length; i++) {
        if (this.playerSequence[i] !== this.sequence[i]) {
          this.playerSequence = []
          this.centerButton = "Wrong!"
          this.playSound(5)
          this.isWrong = true
          setTimeout(() => {
            this.centerButton = "RESET"
            this.isWrong = false
          }, 1000);
          this.showSequence(true)
        }
      }
      if (this.playerSequence.length === this.sequence.length) {
        this.playerSequence = []
        this.round++
        if (this.round === 20) {
          this.centerButton = "Winner!"
          this.isClickable = false
          this.isWon = true
        } else {
          this.showSequence()
        }
      }
    },
    showActive(tile) {
      this.isActive[tile] = true
      setTimeout(() => {
        this.isActive[tile] = false
      }, 300);
    },
    showSequence(redo) {
      let currentIndex = 0
      let speed = this.sequence.length === 0 ? 1000 : this.getModeSpeed(this.gameMode)
      this.isClickable = false
      if (!redo) {
        this.sequence.push(Math.floor(Math.random() * 4 + 1))
      }
      this.sequenceInterval = setInterval(() => {
        if (currentIndex >= this.sequence.length) {
          clearInterval(this.sequenceInterval)
          return this.isClickable = true
        }
        this.playSound(this.sequence[currentIndex])
        this.showActive(this.sequence[currentIndex])
        currentIndex++
      }, speed)
    },
    getModeSpeed(mode) {
      if (mode === 'easy') {
        return 1500
      }
      else if (mode === 'normal') {
        return 1000
      }
      else {
        return 400
      }
    }
  }
}
</script>

<style lang="scss" scoped>
$red: rgb(247, 76, 76);
$yellow: rgb(243, 243, 79);
$green: rgb(46, 247, 46);
$blue: rgb(59, 59, 247);

body {
  background-color: #718087;
}
.game {
  display: flex;
  flex-direction: column;
  align-items: center;
  .game__content {
    background-color: rgb(63, 56, 56);
    width: 300px;
    height: 300px;
    border-radius: 50%;
    display: grid;
    grid-template-columns: 1fr 1fr;
    grid-template-rows: 1fr 1fr;
    .game__item {
      cursor: pointer;
      border: 3px solid rgb(63, 56, 56);
      &:hover {
        opacity: 0.6;
      }
    }
    .game__item_1 {
      background-color: $blue;
      border-top-left-radius: 100%;
      &.active {
        background: lighten($blue, 25%);
      }
    }
    .game__item_2 {
      background-color: $red;
      border-top-right-radius: 100%;
      &.active {
        background: lighten($red, 25%);
      }
    }
    .game__item_3 {
      background-color: $yellow;
      border-bottom-left-radius: 100%;
      &.active {
        background: lighten($yellow, 25%);
      }
    }
    .game__item_4 {
      background-color: $green;
      border-bottom-right-radius: 100%;
      &.active {
        background: lighten($green, 25%);
      }
    }
  }

  .game__start-btn {
    background-color: #c5bbbb;
    width: fit-content;
    padding: 15px;
    border-radius: 10px;
    margin-top: 20px;
    cursor: pointer;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 10px;
    &:hover {
      background-color: #b8aeae;
    }
  }

  .game__mode {
    background-color: #394432;
    padding: 20px;
    margin-top: 20px;
    border-radius: 5px;
    p {
      text-align: center;
      color: #f2f2f2;
      font-size: 18px;
      margin-top: 0;
    }
    button {
      cursor: pointer;
      background-color: rgb(177, 219, 196);
      font-size: 14px;
      letter-spacing: 1px;
      padding: 7px;
      &.active {
        background: $green;
      }
    }
    button + button {
      margin-left: 10px;
    }
  }
}
</style>




