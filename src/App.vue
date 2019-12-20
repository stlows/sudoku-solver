<template>
  <div id="app">
    <div class="col">
      <template v-if="cells.length > 0">
      <div class="grid">
        <square v-for="(square, key) in squares" :key="key" :cells="square" :square="key"></square>
      </div>
    </template>
    <template v-else><span>Loading...</span></template>
    </div>
    <div class="col">
    <button @click="step">Step</button>
    <ol>
      <li v-for="h in history" :key="h.timestamp">
        <span v-if="!h.try">Cell ({{h.cell.row}}, {{h.cell.col}}) filled with a {{h.value}} since it was the only possibility.</span>
        <span v-else>Trying to fill cell ({{h.cell.row}}, {{h.cell.col}}) with a {{h.value}}.</span>
      </li>
    </ol>
    </div>
  </div>
</template>

<script>
import Square from "./components/square";
import axios from "axios";
export default {
  components: { Square },
  data() {
    return {
      cells: [],
      backtrackingTree: 
        { 
          id: new Date().valueOf(),
          value: "Backtracking tree",
          children: []
        }
      ,
      history: [],
      logs: []
    };
  },
  computed: {
    columns() {
      return this.groupBy(this.cells, cell => cell.col);
    },
    rows() {
      return this.groupBy(this.cells, cell => cell.row);
    },
    squares() {
      return this.groupBy(this.cells, cell => cell.square);
    },
    validated() {
      return this.cells.filter(cell => cell.validated);
    },
    notValidated() {
      return this.cells.filter(cell => !cell.validated);
    },
    notValidatedAndEmpty() {
      return this.cells.filter(cell => !cell.validated && cell.value === 0);
    },
    // onePossible(){
    //   return this.cells.filter(cell => cell.possibles.length === 1)
    // }
  },
  methods: {
    log(msg){
      this.logs.push({date: new Date(), msg});
    },
    getSquare(row, col) {
      var square = 0;
      for (var r = 3; r <= 9; r += 3) {
        for (var c = 3; c <= 9; c += 3) {
          if (row < r && col < c) {
            return square;
          }
          square++;
        }
      }
    },
    groupBy(xs, f) {
      return xs.reduce(function(acc, curr) {
        (acc[f(curr)] = acc[f(curr)] || []).push(curr);
        return acc;
      }, {});
    },
    computeAllPossibles(){
      for(var i = 0; i < this.notValidatedAndEmpty.length; i++){
        this.computeCellPossibles(this.notValidatedAndEmpty[i])
      }
    },
    computeCellPossibles(cell){
      cell.possibles = [1,2,3,4,5,6,7,8,9]
      var row = this.rows[cell.row].filter(c => c.value > 0)
      var col = this.columns[cell.col].filter(c => c.value > 0)
      var square = this.squares[cell.square].filter(c => c.value > 0)
      this.handlePossibles(cell,row)
      this.handlePossibles(cell,col)
      this.handlePossibles(cell,square)
      this.handlePossibles(cell, cell.triedAndNotGood)
    },
    handlePossibles(cell, block){
      for(var i = 0; i < block.length; i++){
        if(cell.possibles.includes(block[i].value)){
          cell.possibles.splice(cell.possibles.indexOf(block[i].value), 1);
        }
      }
    },
    step(){
      // var cell = null;
      // if(this.onePossible.length > 0){
      //   cell = this.onePossible[0];
      //   cell.value = cell.possibles[0]
      //   cell.possibles = []
      //   cell.validated = true
      //   this.history.push({
      //     timestamp: new Date().valueOf(),
      //     cell,
      //     value: cell.possibles[0],
      //     try: false
      //   })
      // }
      // else{
        var cell = this.notValidatedAndEmpty[0]
        this.computeCellPossibles(cell)
        cell.value= cell.possibles[0]
        this.history.push({
          timestamp: new Date().valueOf(),
          cell,
          value: cell.possibles[0],
          try: true
        })
      // }
      // this.computeAllPossibles()
    }
  },
  created() {
    axios
      .get("https://sugoku.herokuapp.com/board?difficulty=hard")
      .then(res => {
        for (var row = 0; row < res.data.board.length; row++) {
          for (var col = 0; col < res.data.board[row].length; col++) {
            var validated = res.data.board[row][col] > 0
            this.cells.push({
              value: res.data.board[row][col],
              row: row,
              col: col,
              square: this.getSquare(row, col),
              validated: validated,
              //possibles: validated ? [] : [1,2,3,4,5,6,7,8,9],
              triedAndNotGood: []
            });
          }
        }
        //this.computeAllPossibles()
      });
  }
};
</script>

<style lang="scss">
@import "@/scss/_variables.scss";
#app {
  display: grid;
  grid-template-columns: 400px 1fr;
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
.grid {
  margin: auto;
  display: grid;
  grid-template-columns: repeat(3, 3 * $cellSize);
  grid-template-rows: repeat(3, 3 * $cellSize);
  grid-gap: $squareBorderWidth;
}
</style>
