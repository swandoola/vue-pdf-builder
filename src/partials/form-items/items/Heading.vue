<template>
  <div
    class="mb-3 border p-3"
    style="position:relative"
    @click="selectThisItem"
    :class="isTheItemSelected(item.id)"
  >
    <span
      style="position: absolute; bottom: .15rem; right: .15rem; z-index: 999;"
      v-tooltip.bottom="'Delete this item'"
    >
      <button class="btn btn-xs btn-light noprint" @click="deleteItemFromCol(item)">
        <i class="far fa-trash"></i>
      </button>
    </span>
    <div :class="'text-' + item.options.align">
      <h2 class="mb-2" v-show="item.label">{{ item.label }}</h2>
    </div>
  </div>
</template>

<script>
export default {
  props: [
    "item",
    "colKey",
    "templateItems",
    "loadedTemplate",
    "currentSelectedRow",
    "currentSelectedColKey",
    "currentSelectedCol",
    "currentSelectedItem",
  ],
  methods: {
    isTheItemSelected(itemId) {
      if (this.currentSelectedItem && this.currentSelectedItem.id == itemId) {
        return "border-danger";
      }
      return "border-light";
    },
    selectThisItem() {
      this.$emit("updateCurrentSelectedItem", this.item);
      // this.currentSelectedItem = null;
      // this.currentSelectedItem = this.item;
    },
    deleteItemFromCol(item) {
      var data = {
        colKey: this.colKey,
        item: item,
      };
      var indexOfRowToRemoveFrom = this.templateItems
        .map(function (x) {
          return x.id;
        })
        .indexOf(this.currentSelectedRow.id);

      var templateItems = this.templateItems;
      var rowToAddTo = templateItems[indexOfRowToRemoveFrom].items;

      var indexToRemove = rowToAddTo[data.colKey].items
        .map(function (x) {
          return x.id;
        })
        .indexOf(data.item.id);

      rowToAddTo[data.colKey].items.splice(indexToRemove, 1);
      console.log("saveTemplate");
    },
  },
};
</script>

<style>
</style>