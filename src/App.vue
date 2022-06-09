<template>
  <h2 class="text-center mt-4 mb-4 text-white">Tic Tac Toe</h2>

  <!-- Gameboard -->
  <div class="game-board d-flex flex-column justify-content-center align-items-center">
    <div class="r1 d-flex">
      <button class="cell" :class="{red:isRed('tl'), blue:isBlue('tl')}" @click="change('tl')" :disabled="gameStop"></button>
      <button class="cell mx-1" :class="{red:isRed('tm'), blue:isBlue('tm')}" @click="change('tm')" :disabled="gameStop"></button>
      <button class="cell" :class="{red:isRed('tr'), blue:isBlue('tr')}" @click="change('tr')" :disabled="gameStop"></button>
    </div>
    <div class="r2 d-flex my-1">
      <button class="cell" :class="{red:isRed('cl'), blue:isBlue('cl')}" @click="change('cl')" :disabled="gameStop"></button>
      <button class="cell mx-1" :class="{red:isRed('cm'), blue:isBlue('cm')}" @click="change('cm')" :disabled="gameStop"></button>
      <button class="cell" :class="{red:isRed('cr'), blue:isBlue('cr')}" @click="change('cr')" :disabled="gameStop"></button>
    </div>
    <div class="r3 d-flex">
      <button class="cell" :class="{red:isRed('bl'), blue:isBlue('bl')}" @click="change('bl')" :disabled="gameStop"></button>
      <button class="cell mx-1" :class="{red:isRed('bm'), blue:isBlue('bm')}" @click="change('bm')" :disabled="gameStop"></button>
      <button class="cell" :class="{red:isRed('br'), blue:isBlue('br')}" @click="change('br')" :disabled="gameStop"></button>
    </div>

    <!-- Winning Line -->
    <div class="winner-line" :class="'wl-' + winnerLinePos"></div>
  </div>

  <!-- Turn and Switch -->
  <div class="d-flex flex-column align-items-center justify-content-center mt-4">
    <h4 class="text-white"> <strong :style="{color: color}">{{player}}</strong> Player's Turn</h4>
    <div>
      <button @click="switchPlayer('Red')" class="btn btn-danger cross"></button>
      <button @click="switchPlayer('Blue')" class="btn btn-primary circle" style="margin:0 5px"></button>
      <button @click="resetBoard" class="btn reset-btn">Reset</button>
    </div>
  </div>

  <!-- Game Information -->
  <div class="d-flex flex-column align-items-center justify-content-center mt-4">
    <h4 class="text-center text-white">Game Info</h4>
    <p class="text-center text-white">{{board}}</p>
    <p class="text-center text-white" v-html="message"></p>
    <button v-if="gameStop" @click="resetBoard" class="btn reset-btn">Reset</button>
  </div>
</template>

<script>

  // Check if there is any winner
  function checkBoard(b) {
    if (b.tl == b.tm && b.tl == b.tr && ![b.tm,b.tl,b.tr].includes(0)) { return "ht" }
    else if (b.cl == b.cm && b.cl == b.cr && ![b.cm, b.cl, b.cr].includes(0)) { return "hc" }
    else if (b.bl == b.bm && b.bl == b.br && ![b.bm, b.bl, b.br].includes(0)) { return "hb" }
    
    else if (b.tl == b.cl && b.tl == b.bl && ![b.cl, b.tl, b.bl].includes(0)) { return "vl" }
    else if (b.tm == b.cm && b.tm == b.bm && ![b.cm, b.tm, b.bm].includes(0)) { return "vm" }
    else if (b.tr == b.cr && b.tr == b.br && ![b.cr, b.tr, b.br].includes(0)) { return "vr" }
    
    else if (b.cm == b.tr && b.cm == b.bl && ![b.tr, b.cm, b.bl].includes(0)) { return "du" }
    else if (b.cm == b.tl && b.cm == b.br && ![b.tl, b.cm, b.br].includes(0)) { return "dd" }
    
    else {return false}
  }

  // Check if the board is a draw
  function isDraw(b) {
    if (![b.tl, b.tm, b.tr, b.cl, b.cm, b.cr, b.bl, b.bm, b.br].includes(0)) {
      return true
    } else { return false }
  }

  // Mapping the position played for the Game Info
  function mapPos(p) {
    const mapper = {
      tl: "Top Left", tm: "Top Middle", tr: "Top Right",
      cl: "Center Left", cm: "Center Middle", cr: "Center Right",
      bl: "Bottom Left", bm: "Bottom Middle", br: "Bottom Right"
    }
    return mapper[p]
  } 

  export default {
    data() {
      return {
        board: {
          tl: 0, tm: 0, tr: 0,
          cl: 0, cm: 0, cr: 0,
          bl: 0, bm: 0, br: 0
        },
        // Red: 1, Blue: 2
        player: "Red",
        color: "#DC3545",
        message: "Game Starting...<br>",
        gameStop: false,
        winnerLinePos: "draw"
      }
    },
    methods: {
      // Switch player
      switchPlayer(c) {
        this.player = c
        switch (c) {
          case ("Red"): this.color = "#DC3545"; break;
          case ("Blue"): this.color = "#0D6EFD"; break;
        }
      },

      // When a cell in clicked...
      change(p) {
        if (this.board[p] == 0) {
          if (this.player == "Red") {
            this.board[p] = 1
            this.player = "Blue"
            this.color = "#0D6EFD"
            const checkResult = checkBoard(this.board)
            
            // Check the game state: winner, draw / continue
            if (checkResult) {
              this.message += "<strong class='text-danger'>Red is the Winner!</strong><br>"
              this.gameStop = true
              this.winnerLinePos = checkResult
            } else if (isDraw(this.board)) {
              this.message += "<strong>It's a Draw!<strong>"
              this.gameStop = true
            } else {
              this.message += `
                <strong class="text-danger">Red</strong> 
                placed in <strong class="text-secondary">
                ${mapPos(p)}</strong>
              <br>`
            }

          } else if (this.player == "Blue") {
            this.board[p] = 2
            this.player = "Red"
            this.color = "#DC3545"
            const checkResult = checkBoard(this.board)
            
            if (checkResult) {
              this.message += "<strong class='text-primary'>Blue is the Winner!</strong><br>"
              this.gameStop = true
              this.winnerLinePos = checkResult
            } else if (isDraw(this.board)) {
              this.message += "<strong>It's a Draw!<strong>"
              this.gameStop = true
            } else {
              console.log('h')
              this.message += `
                <strong class="text-primary">Blue</strong> 
                placed in <strong class="text-secondary">
                ${mapPos(p)}</strong>
              <br>`
            }

          }
        } else {
          this.message += "Cell Taken!<br>"
        }
      },
      resetBoard() {
        this.board = {
          tl: 0, tm: 0, tr: 0,
          cl: 0, cm: 0, cr: 0,
          bl: 0, bm: 0, br: 0
        }
        this.message = "Game Starting...<br>"
        this.gameStop = false
        this.winnerLinePos = "draw"
      },
      isRed(p) {
        return (this.board[p] == 1)
      },
      isBlue(p) {
        return (this.board[p] == 2)
      }
    }
  }
</script>

<style scoped>
  .cell {
    margin: 0;
    height: 50px;
    width: 50px;
    background-color: #383838;
    border: 0px;
    border-radius: 5px !important;
  }

  .cell:hover {
    background-color: #484848;
  }

  .blue {
    background-color: #0D6EFD !important;
    background: url(./assets/circle.png) no-repeat center;
    background-size: 35px;
  }

  .red {
    background-color: #DC3545 !important;
    background: url(./assets/cross.png) no-repeat center;
    background-size: 35px;
  }

  .cross {
    height: 38px;
    width: 38px;
    background-color: #DC3545 !important;
    background: url(./assets/cross.png) no-repeat center;
    background-size: 25px;
  }

  .circle {
    height: 38px;
    width: 38px;
    background-color: #0D6EFD !important;
    background: url(./assets/circle.png) no-repeat center;
    background-size: 25px;
  }

  .reset-btn {
    background-color: #383838;
    color: white;
  }

  .reset-btn:hover {
    background-color: #585858;
    color: white;
  }

  .game-board {
    position: relative;
  }

  .winner-line {
    position: absolute;
    background: #383838;
    border-radius: 5px;
  }

  /* wl: winner line, h: horizontal, v: vertical, d: diagonal */
  .wl-ht { margin-top: -5px; width: 150px; height: 10px; top: 16.6%; }
  .wl-hc { margin-top: -5px; width: 150px; height: 10px; top: 50%; }
  .wl-hb { margin-top: -5px; width: 150px; height: 10px; top: 83.4%; }

  .wl-vl { width: 10px; height: 150px; left: calc(50% - 58px); }
  .wl-vm { width: 10px; height: 150px; }
  .wl-vr { width: 10px; height: 150px; left: calc(50% + 49px); }

  .wl-du { margin-top: 4px; width: 10px; height: 200px; transform: rotate(45deg); }
  .wl-dd { margin-top: 4px; width: 10px; height: 200px; transform: rotate(-45deg); }
  .wl-draw { display: none }
</style>