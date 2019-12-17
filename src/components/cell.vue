<template>
  <div class="cell" @click="cellClicked" :class="{selected}">
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
  methods: {
    cellClicked() {
      this.cell.selected = !this.cell.selected;
      this.selected = !this.selected;
    }
  }
};
</script>

<style lang="scss">
$border-color: #aaa;
.cell {
  cursor: pointer;
  display: flex;
  justify-content: center;
  text-align: center;
  align-items: center;
  border-top: 1px solid $border-color;
  border-left: 1px solid $border-color;
}
.cell:nth-of-type(9n) {
  border-right: 1px solid $border-color;
}
.cell.selected {
  border: 2px solid rgb(37, 29, 149);
}
@for $i from 73 through 81 {
  .cell:nth-child(#{$i}) {
    border-bottom: 1px solid $border-color;
  }
}
</style>