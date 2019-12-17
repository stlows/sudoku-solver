<template>
  <div class="grid">
    <template v-for="row in cells">
      <cell v-for="cell in row" :key="cell.id" :cell="cell"></cell>
    </template>
  </div>
</template>

<script>
import Cell from "./cell";
import axios from "axios";
export default {
  components: { Cell },
  data() {
    return {
      cells: []
    };
  },
  created() {
    axios
      .get("https://sugoku.herokuapp.com/board?difficulty=easy")
      .then(res => {
        this.cells = res.data.board.map(row =>
          row.map(cell => {
            return {
              value: cell
            };
          })
        );
      });
  }
};
</script>

<style lang="scss">
$cell-size: 36px;
.grid {
  margin: auto;
  display: grid;
  grid-template-columns: repeat(9, $cell-size);
  grid-template-rows: repeat(9, $cell-size);
}
</style>