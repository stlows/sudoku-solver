<template>
  <div>
    <div v-if="cells.length > 0">
      <div class="grid">
        <square v-for="(square, key) in squares" :key="key" :cells="square" :square="key"></square>
      </div>
      <pre>
        {{squares}}
      </pre>
    </div>

    <div v-else>Loading...</div>
  </div>
</template>

<script>
import Square from "./square";
import axios from "axios";
export default {
  components: { Square },
  data() {
    return {
      cells: []
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
    }
  },
  methods: {
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
    }
    //possibles(row, col) {}
  },
  created() {
    axios
      .get("https://sugoku.herokuapp.com/board?difficulty=easy")
      .then(res => {
        for (var row = 0; row < res.data.board.length; row++) {
          for (var col = 0; col < res.data.board[row].length; col++) {
            this.cells.push({
              value: res.data.board[row][col],
              row: row,
              col: col,
              square: this.getSquare(row, col),
              validated: res.data.board[row][col] > 0
            });
          }
        }
      });
  }
};
</script>

<style lang="scss">
$cellSize: 36px;
$squareBorderColor: black;
$squareBorderWidth: 2px;

$cellBorderColor: #aaa;
$cellBorderWidth: 1px;

.grid {
  margin: auto;
  display: grid;
  grid-template-columns: repeat(3, 3 * $cellSize);
  grid-template-rows: repeat(3, 3 * $cellSize);
  grid-gap: $squareBorderWidth;
}
.square {
  z-index: 3;
  display: grid;
  grid-template-columns: repeat(3, $cellSize);
  grid-template-rows: repeat(3, $cellSize);
  box-shadow: 0 0 0 $squareBorderWidth $squareBorderColor;
  grid-gap: $cellBorderWidth;
}
.cell {
  cursor: pointer;
  display: flex;
  justify-content: center;
  text-align: center;
  align-items: center;
  box-shadow: 0 0 0 $cellBorderWidth $cellBorderColor;
}
</style>