<template>
  <div class="cell" @click="cellClicked" :class="{...borders, selected}">
    <span v-if="cell.value > 0">{{cell.value}}</span>
  </div>
</template>

<script>
export default {
  props: ["cell"],
  data() {
    return {
      selected: false
    };
  },
  computed: {
    borders(){
      return {
        br: (this.cell.col % 3) < 2,
        bb: (this.cell.row % 3) < 2
      }
    }
  },
  methods: {
    cellClicked() {
      this.cell.selected = !this.cell.selected;
      this.selected = !this.selected;
    }
  }
};
</script>

<style lang="scss">
@import "@/scss/_variables.scss";
.cell {
  cursor: pointer;
  display: flex;
  justify-content: center;
  text-align: center;
  align-items: center;
  &.br {
    border-right: 1px solid #aaa;
  }
  &.bb{
    border-bottom: 1px solid #aaa;
  }
  &.selected {
    border: 2px solid rgb(37, 29, 149);
  }
}

</style>