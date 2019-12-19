<template>
  <div class="square">
    <cell v-for="cell in cells" :key="'row_' + cell.row + '_col_' + cell.col" :cell="cell"></cell>
  </div>
</template>

<script>
import Cell from "./cell";
export default {
  components: { Cell },
  props: ["cells", "square"],
  computed: {}
};
</script>

<style lang="scss">
@import "@/scss/_variables.scss";
.square {
  z-index: 3;
  display: grid;
  grid-template-columns: repeat(3, $cellSize);
  grid-template-rows: repeat(3, $cellSize);
  box-shadow: 0 0 0 $squareBorderWidth $squareBorderColor;
}
</style>