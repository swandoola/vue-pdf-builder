<template>
  <div
    class="mb-3 border p-3"
    style="position:relative"
    @click.stop="selectThisItem"
    :class="isTheItemSelected(item.id)"
  >
    <span
      style="position: absolute; bottom: 0rem; right: .15rem; z-index: 999;"
      v-tooltip.bottom="'Delete this item'"
    >
      <button class="btn btn-xs btn-light noprint" @click="deleteItemFromCol(item)">
        <i class="far fa-trash"></i>
      </button>
    </span>
    <div v-if="!inputView">
      <div :class="'text-' + item.options.align">
        <h6 class="mb-0">{{ clientName }}</h6>
      </div>
    </div>
    <div v-if="inputView && client">
      <h6 class="mb-0">{{ clientName }}</h6>
    </div>
  </div>
</template>

<script>
export default {
  props: [
    "item",
    "colKey",
    "inputView",
    "client",
    "templateItems",
    "loadedTemplate",
    "currentSelectedRow",
    "currentSelectedColKey",
    "currentSelectedCol",
    "currentSelectedItem",
    "itemBeingDragged",
  ],
  computed: {
    clientName() {
      if (this.client) {
        this.item.client = this.client;
        return this.client.name;
      }
      return this.item.name;
    },
  },
  methods: {
    isTheItemSelected(itemId) {
      if (
        this.currentSelectedItem &&
        this.currentSelectedItem.id == itemId
      ) {
        return "border-danger";
      }
      return "border-light";
    },
    selectThisItem() {
      this.currentSelectedItem = null;
      this.currentSelectedItem = this.item;
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