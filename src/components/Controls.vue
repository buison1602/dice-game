<template>
  <div class="wrapper-controls">
    <button
      v-on:click="newGame"
      class="control btn-new"><i class="ion-ios-plus-outline"></i>New game</button>

    <button
      v-on:click="rollDice"
      class="control btn-roll"><i class="ion-ios-loop"></i>Roll dice</button>

    <button
      v-on:click="$emit('handleHoldScore')"
      class="control btn-hold"><i class="ion-ios-download-outline"></i>Hold</button>

<!--
    + disabled = "true" thì ta không thể nhập giá trị vào ô 'input' nữa
    + disabled = "false" thì ngược lại

    + v-on:input là 1 event được thực thi khi giá trị nhập vào input thay đổi -> gọi method handleChangeFinalScore ở App.vue
    + giá trị '$event' được truyền vào là event của 'Controls.vue'
    + ràng buộc giá trị của biến value = finalScore
-->
    <input
      v-bind:disabled="isPlaying"
      v-bind:value="finalScore"
      v-on:input="$emit('handleChangeFinalScore', $event)"
      type="number" placeholder="Final score" class="final-score">
  </div>
</template>
<!--
    1. Ràng buộc dữ liệu (Thuộc tính HTML) -> v-bind
        + 1 chiều từ data -> input

    2. Ràng buộc dữ liệu 2 chiều           -> v-model
        + 1 chiều từ data -> input
        + 1 chiều từ input -> data

    Nếu như nó(data) không phải là data trực tiếp của Component Controls -> thì tại Controls.vue
    ta không được phép thay đổi giá trị của nó

    -> Do đó nếu ta dùng ràng buộc dữ liệu 2 chiều ( v-model ) tại Controls thì sẽ  LỖI  : bởi vì
    khi thay đổi giá trị trong ô input(dòng 13) thì giá trị 'finalScore' ở App.vue cũng bị thay đổi

    Lưu ý: dữ liệu được lấy ra từ  ô input thì là chuỗi STRING
-->
<script>
export default {
  name: 'controls',
  props: {
    // khi ta khai báo kiểu dữ liệu là [Number, String] thì giá trị 'finalScore' có thể nhận được
    // giá trị Number và giá trị String
    finalScore: { type: [Number, String], default: 100 },
    isPlaying: { type: Boolean, default: false }
  },
  data() {
    return {

    }
  },
  methods: {
    newGame() {
      // kích hoạt sự kiện 'handleNewGame' của App.vue truyền vào
      this.$emit('handleNewGame')
    },
    rollDice() {
      this.$emit('handleRollDice');
    }
  }
}
</script>

<style>
.control {
  position: absolute;
  width: 200px;
  left: 50%;
  transform: translateX(-50%);
  color: #555;
  background: none;
  border: none;
  font-family: Lato;
  font-size: 20px;
  text-transform: uppercase;
  cursor: pointer;
  font-weight: 300;
  transition: background-color 0.3s, color 0.3s;
}
.control.disable {
  pointer-events: none;
}

.control:hover { font-weight: 600; }
.control:hover i { margin-right: 20px; }

.control:focus {
  outline: none;
}

.control i {
  color: #42b983;
  display: inline-block;
  margin-right: 15px;
  font-size: 32px;
  line-height: 1;
  vertical-align: text-top;
  margin-top: -4px;
  transition: margin 0.3s;
}

.btn-new { top: 45px;}
.btn-roll { top: 403px;}
.btn-hold { top: 467px;}

.final-score {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  top: 520px;
  color: #555;
  font-size: 18px;
  font-family: 'Lato';
  text-align: center;
  padding: 10px;
  width: 160px;
  text-transform: uppercase;
}

.final-score:focus { outline: none; }

</style>
