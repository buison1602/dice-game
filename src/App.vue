<template>
	<div id="app">
        <div class="wrapper clearfix">

            <players
                v-bind:isWinner="isWinner"
                v-bind:activePlayer="activePlayer"
                v-bind:currentScore="currentScore"
                v-bind:scoresPlayer="scoresPlayer"
            />

            <controls
                v-bind:isPlaying="isPlaying"
                v-bind:finalScore="finalScore"
                v-on:handleChangeFinalScore="handleChangeFinalScore"
                v-on:handleHoldScore="handleHoldScore"
                v-on:handleNewGame="handleNewGame"
                v-on:handleRollDice="handleRollDice"
            />

            <dices
                v-bind:dices="dices"
            />

            <popup-rule
                v-on:handleConfirm="handleConfirm"
                v-bind:isOpenPopup="isOpenPopup"
            />
        </div>
	</div>
</template>

<script>
import Players from './components/Players';
import Controls from './components/Controls';
import Dices from './components/Dices';
import PopupRule from './components/PopupRule';

export default {
	name: 'app',
	data () {
		return {
            isPlaying: false,
            isOpenPopup: false,
            activePlayer: 0, //Ai là người chơi hiện tại?
            scoresPlayer: [0, 0],
            dices: [2, 6],
            currentScore: 0,
            finalScore: 10
		}
	},
	components: {
        Players,
        Controls,
        Dices,
        PopupRule
    },
    computed: {
        isWinner() {
            let { scoresPlayer, finalScore } = this;

            if(scoresPlayer[0] >= finalScore || scoresPlayer[1] >= finalScore) {
                // Dung cuoc choi
                this.isPlaying = false;
                return true;
            }
            return false;
        }
    },
    methods: {
        handleChangeFinalScore(e) {
            // hàm parseInt là ép kiểu sang Number
            var number = parseInt(e.target.value);
            if(isNaN(number)) {
                this.finalScore = '';
            } else {
                this.finalScore = number;
            }
        },
        handleHoldScore() {
            if(this.isPlaying) {
                // activePlayer = 0 -> Người chơi 1
                // activePlayer = 1 -> Người chơi 2
                // scorePlayer -> array
                // scorePlayer[0] = scorePlayer[activePlayer]
                // scorePlayer[1] = scorePlayer[activePlayer]

                // destructuring - lấy ra các giá trị hiện tại
                let { scoresPlayer, activePlayer, currentScore } = this;
                let scoreOld = scoresPlayer[activePlayer];
                // let cloneScorePLayer = [...scoresPlayer];
                    // cloneScorePLayer[activePlayer] = scoreOld + currentScore;

            /*
              + Trong Vue nếu thay đổi 'TRỰC TIẾP' giá trị trong mảng thì giá trị mới sẽ không được render lên màn hình
              mà vẫn sử dụng giá trị cũ
              -> Để khắc phục điều đó thì ta có thể tạo 1 mảng mới, thay thế mảng ban đầu bằng mảng mới

              -> Hoặc là dùng hàm $set như bên dưới
            */

            // $set là 1 hàm thay đổi giá trị của phần tử trong ARRAY
                // this.scoresPlayer là mảng
                // activePlayer là vị trí phần tử muốn thay đổi giá trị
                // scoreOld + currentScore là giá trị mới
                this.$set(this.scoresPlayer, activePlayer, scoreOld + currentScore);
                // this.scoresPlayer = cloneScorePLayer;

                // Nếu như không có người nào thắng thì mới tiến hành thay đổi lượt chơi
                if(!this.isWinner) {
                    this.nextPlayer();
                }
                // this.scoresPlayer[this.activePlayer] = this.scoresPlayer[this.activePlayer] + this.currentScore;
                // this.scoresPlayer[activePlayer] = scoreOld + currentScore;
                // console.log(cloneScorePLayer);
            } else {
                alert('Vui lòng nhất vào nút NewGame');
            }
        },
        nextPlayer() {
            // activePlayer = 0 -> 1  ===== 1 -> 0
            this.activePlayer = this.activePlayer === 0 ? 1 : 0;
            this.currentScore = 0;
        },
        handleRollDice() {
            if(this.isPlaying) {
                // Xoay xúc xắc
                // Math.random(): 0 -> 1
                /*
                0 <= X <= 1
                0 <= (Y = X * 6) <= 6
                */
                var dice1 = Math.floor(Math.random() * 6) + 1;
                var dice2 = Math.floor(Math.random() * 6) + 1;

                // thay đổi lại địa chỉ mảng với các giá trị trong mảng là các số ngẫu nhiên vừa quay được
                this.dices = [dice1, dice2];

                if(dice1 === 1 || dice2 === 1) {
                    let activePlayer = this.activePlayer;
                    // tại đây có thể dùng arrow function
                    // setTimeout( () => {console.log('hahahah')} )
                    setTimeout(function() {
                        alert(`Nguoi choi Player ${activePlayer + 1} đã quay trúng số 1. Rất tiếc`);
                    }, 10)

                    this.nextPlayer();
                } else {
                    // Cộng dồn vào điểm tạm thời cho người đang chơi
                    this.currentScore = this.currentScore + dice1 + dice2;
                }
            } else {
                alert('Vui lòng nhất vào nút NewGame');
            }
        },
        handleConfirm() {
            this.isPlaying = true;
            this.isOpenPopup = false;
            this.activePlayer = 0;
            this.dices = [1, 1];
            this.scoresPlayer = [0, 0];
            this.currentScore = 0;
        },
        handleNewGame() {
            // Hiển thị Popup -> Show luật chơi
            this.isOpenPopup = true;
        }
    }
}
</script>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;

}

.clearfix::after {
    content: "";
    display: table;
    clear: both;
}

body {
    background-image: linear-gradient(rgba(62, 20, 20, 0.4), rgba(62, 20, 20, 0.4)), url('/public/assets/back.jpg');
    background-size: cover;
    background-position: center;
    font-family: Lato;
    font-weight: 300;
    position: relative;
    height: 100vh;
    color: #555;
}

.wrapper {
    width: 1000px;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: #fff;
    box-shadow: 0px 10px 50px rgba(0, 0, 0, 0.3);
    overflow: hidden;
}
</style>
