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
  computed: {},
  methods: {
    cellClicked() {
      this.cell.selected = !this.cell.selected;
      this.selected = !this.selected;
    }
  }
};
</script>

<style lang="scss">
.cell.selected {
  border: 2px solid rgb(37, 29, 149);
}
</style>