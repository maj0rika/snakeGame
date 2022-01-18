<template>
  <div>
    <canvas
      id="스네이크"
      ref="스네이크"
      :width="boardSizePx"
      :height="boardSizePx"
    >
    </canvas>
  </div>
</template>

<script>
import constants from "./constants";
export default {
  name: "Snake",
  props: {
    pxSize: Number,
    boardSize: Number,
    speed: Number,
    playing: Boolean,
    gameStart: Function,
    addScores: Function,
    scores: Number,
    snakeColor: Number,
  },
  data() {
    return {
      speedUpdata: this.speed,
    };
  },
  computed: {
    boardSizePx() {
      return this.pxSize * this.boardSize;
    },
  },
  // beforeCreate
  // created
  // beforeMount
  // mounted
  // beforeUpdate
  // updated
  // activated
  // deactivated
  // beforeDestroy
  // destroyed
  // errorCaptured
  mounted() {
    window.addEventListener("keydown", this.pressKey);
    this.con = document.getElementById("스네이크");
    //this.con = document.querySelector("canvas");
    this.boardContext = this.con.getContext("2d");
    console.log("this.boardContext", this.boardContext);
  },
  created() {
    this.resetSnake();
  },
  // beforeDestroy() {
  //     window.removeEventListener('keydown',this.pressKey);
  // },
  watch: {
    playing(value) {
      console.log(value);
      if (value) {
        this.resetSnake(); //리셋
        this.move(); //움직임
      }
    },
  },
  methods: {
    resetSnake() {
      //중앙 픽셀값 x,y에 저장
      console.log(" this = ", this);
      this.snake = [
        {
          x: this.getCenterPx(),
          y: this.getCenterPx(),
        },
      ];
      this.direction = constants[0];
      console.log(" this.direction = ", this.direction);
      this.targetPx = null;
    },
    getCenterPx() {
      return Math.round(this.boardSize / 2);
    },
    move() {
      if (!this.playing) {
        return;
      }
      this.clear(); //모두 지우기
      this.applePx();
      const forwardHeadPx = {
        x: this.snake[0].x + this.direction.move.x,
        y: this.snake[0].y + this.direction.move.y,
      };
      console.log("머리 좌표", forwardHeadPx);
      if (
        this.boardOut(forwardHeadPx) ||
        this.countPixelInSnake(this.snake[0]) > 1
      ) {
        this.gameStart();
        alert(`Game over! 당신의 점수는 ${this.scores}점 입니다.`);
      }

      if (this.TargetNewHead()) {
        this.snake.unshift(this.targetPx);
        this.targetPx = null;
        this.addScores(this.speed);
        this.$emit("speedUpdate", this.snake.length);
      } else {
        // console.log(" forwardHeadPx = ",forwardHeadPx);
        this.snake.unshift(forwardHeadPx); //전진, 배열 앞에 추가
        // console.log(" this.snake.unshift(forwardHeadPx) = ",this.snake.unshift(forwardHeadPx));
        this.snake.pop(); //꼬리 지우기. 배열 맨뒤 삭제
        // console.log(" this.snake.pop()= ",this.snake.pop());
      }

      this.boardContext.beginPath(); //그리기시작
      // console.log(" beginPath= ",this.boardContext.beginPath());
      this.snake.forEach(this.drawPx); //각 요소 그리기

      this.boardContext.closePath(); //그리기끝

      setTimeout(this.move, this.getMoveDelay()); //움직임 반복
    },
    drawPx({ x, y }) {
      this.boardContext.rect(
        //x, y, x길이, y길이
        x * this.pxSize,
        y * this.pxSize,
        this.pxSize,
        this.pxSize
      );
      this.boardContext.fillStyle = this.Color();
      this.boardContext.fill(); //채우기
    },
    Color() {
      if (this.snakeColor) {
        return "#" + Math.floor(Math.random() * 0xffffff).toString(16);
      } else {
        return "green";
      }
    },
    getMoveDelay() {
      // 뱀 움직임 딜레이
      return (2 / Number(this.speed)) * 1000;
    },
    clear() {
      //지우기
      this.boardContext.clearRect(0, 0, this.boardSizePx, this.boardSizePx);
    },
    boardOut({ x, y }) {
      // 보드 탈출 감지

      return x < 0 || y < 0 || x >= this.boardSize || y >= this.boardSize;
    },
    pressKey(event) {
      //const newDirection = constants.find((f)=>{return f.keyCode  === event.keyCode});
      const newDirection = constants.find((f) => f.keyCode === event.keyCode);
      console.log("f=>f.keyCode", (f) => f.keyCode);
      console.log("event.keyCode", event.keyCode);
      if (!newDirection) {
        return;
      }

      if (Math.abs(newDirection.keyCode - this.direction.keyCode) !== 2) {
        //180도 회전 막기
        this.direction = newDirection;
      }
    },
    applePx() {
      if (!this.targetPx) {
        let targetPx = this.getRandomPx();
        while (this.countPixelInSnake(targetPx) > 0) {
          // 뱀몸속에 먹이 생기기 막기
          targetPx = this.getRandomPx();
        }
        this.targetPx = targetPx;
      }
      this.boardContext.beginPath();
      this.boardContext.rect(
        this.targetPx.x * this.pxSize, //픽셀 사이즈만큼 좌표 이동
        this.targetPx.y * this.pxSize,
        this.pxSize,
        this.pxSize
      );
      this.boardContext.fillStyle = "red";
      this.boardContext.fill();
      this.boardContext.closePath();
    },
    getRandomPx() {
      return {
        x: Math.floor(Math.random() * this.boardSize), //ex 보드칸 10칸이면 0~9까지
        y: Math.floor(Math.random() * this.boardSize),
      };
    },
    countPixelInSnake(Px) {
      //  console.log(this.snake.filter(({ x, y }) => x === Px.x && y === Px.y).length);
      console.log("this.snake", this.snake);
      return this.snake.filter(({ x, y }) => x === Px.x && y === Px.y).length;
    },
    TargetNewHead() {
      return (
        this.snake[0].x + this.direction.move.x === this.targetPx.x &&
        this.snake[0].y + this.direction.move.y === this.targetPx.y
      );
    },
  },
};
</script>

<style>
#스네이크 {
  border: 1px solid #ccc;
}
</style>
