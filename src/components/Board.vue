<template>
  <div class="board-outer">
    <v-btn-toggle v-model="opened_squares" multiple>
      <div class="board">
        <v-btn  v-for="element in array"  v-on:click="open(element)">
          <!-- <div class="board-item"> -->
          <!-- {{element}} -->
          <!-- </div> -->
          <div> {{squares[element]}} </div>
        </v-btn>
      </div>
    </v-btn-toggle>
  </div>
</template>

<script>
export default {
  name: "Board",
  data() {
    return {
      array: [...Array(64).keys()],
      squares: [...Array(64).keys()].fill(''),
      bombs: [],
      opened_squares: [],
      gameover: false
    };
  },
  created: function() {
      this.getBoard()
  },
  methods: {
    open: function(element) {
        if (this.gameover) {
            return
        }
        if (this.bombs.includes(element)) {
            this.gameover = true;
            alert('you lost')
        } else if (this.squares[element] === '') {
            const bombs_around = this.getNeighbors(element)
                .filter(x => this.bombs.includes(x)).length;
            this.squares[element] = bombs_around
            if (bombs_around == 0) {
                this.getNeighbors(element).forEach(el => {
                    this.opened_squares.push(el)
                    this.open(el)
                })
            }
        }
    },
    getNeighbors: function(n) {
        const indices = [-1,0,1]
        const res = [];
        indices.forEach(x => {
            indices.forEach(y => {
                if (this.within_bounds(n, x, y)) {
                    res.push(n + x + 8*y)
                }
            })
        })
        return res.filter(x => x >= 0 && x < 64)
    },
    within_bounds: function(n, x, y) {
        return !(x==0 && y==0) && (n % 8 + x < 8) && (n % 8 + x >= 0)
    },
    getBoard: function() {
       this.array.forEach(n => {
            if(Math.floor(Math.random() * 100) < 10) {
                    this.bombs.push(n)
                }
        })
    }
  }
};
</script>

<style>
.board {
  grid-template-columns: repeat(8, 40px [col-start]);
  display: grid;
  padding: 5px;
}
.board-outer {
  width: 240px;
  margin: auto;
}
.board-item {
    height: 60px;
}
</style>