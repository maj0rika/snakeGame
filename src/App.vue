<template>
  <div class="컨테이너">
    <div class="내용">
      <h1>Snake Game</h1>
      <div class="입력_컨테이너">
        <div class="입력_박스">
          픽셀 사이즈 :
          <input type="number" min="5" v-model="pxSize" />
        </div>
        <div class="입력_박스">
          보드사이즈 :
          <input type="number" min="10" v-model="boardSize" />
        </div>
        <div class="입력_박스">
          스피드 :
          <input type="number" min="1" v-model="speed" />
        </div>
      </div>
      <Snake
        :pxSize="pxSize"
        :boardSize="boardSize"
        :speed="speed"
        :playing="playing"
        :gameStart="gameStart"
        :addScores="addScores"
        :scores="scores"
        :snakeColor="snakeColor"
        :hardMode="hardMode"
        @speedUpdate="speedUpdate"
      />
      <div class="버튼_박스">
        <button id="색_버튼" v-on:click="changeColor()">
          {{ this.snakeColor ? "눈뽕" : "초록뱀 " }}
        </button>
        <button id="색_버튼" v-on:click="changeMode()">
          {{ this.hardMode ? "하드모드" : "일반모드 " }}
        </button>
      </div>
      <div>점수 : {{ scores }}</div>
      <div class="버튼_박스">
        <button id="게임시작_버튼" v-on:click="gameStart()">
          {{ this.playing ? "게임종료" : "게임시작" }}
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import Snake from "./components/Snake.vue";

export default {
  name: "App",
  components: {
    Snake,
  },
  data() {
    return {
      pxSize: 5,
      boardSize: 10,
      speed: 10,
      scores: 0,
      playing: false,
      snakeColor: false,
      hardMode: false,
    };
  },
  methods: {
    speedUpdate(length) {
      console.log("length", length);
      this.speed = this.speed * 1.1;
    },
    gameStart() {
      this.speed = 10;
      this.scores = 0;
      this.playing = !this.playing;
      console.log("toggle");
    },
    addScores(scores) {
      this.scores += Math.floor(scores);
    },
    changeColor() {
      this.snakeColor = !this.snakeColor;
    },
    changeMode() {
      this.hardMode = !this.hardMode;
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.컨테이너 {
  display: flex;
  flex-flow: row;

  justify-content: center;
  height: 80vh;
}
.내용 {
  display: flex;
  flex-flow: column;
}
.입력_컨테이너 {
  display: flex;
  flex-flow: row;
  justify-content: space-around;
}
.입력_박스 {
  width: 150px;
  background-color: #dbe0e6;
  border-radius: 4px;
  padding: 5px;
  margin: 5px;
}
.입력_박스 input {
  width: 30px;
  border-radius: 4px;
  border: 1px solid #dbe3ee;
  margin: 5px;
}
.버튼_박스 {
  display: flex;
  flex-flow: row;

  justify-content: center;
}
#색_버튼 {
  font-size: 7px;
  width: 70px;
  padding: 5px 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin: 10px 0;
}
#색_버튼:active {
  background-color: rgb(99, 96, 96);
}
#게임시작_버튼 {
  font-size: 20px;
  width: 150px;
  padding: 10px 20px;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-top: 10px;
}
#게임시작_버튼:active {
  background-color: rgb(99, 96, 96);
}
</style>
