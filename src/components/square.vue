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
</style>