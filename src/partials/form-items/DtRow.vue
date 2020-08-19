<template>
  <div
    class="row border p-2 unmargin"
    :id="'row-' + row.id"
    :class="isTheRowSelected()"
    @click.stop="selectedRow"
  >
    <div class="col unmargin">
      <!--  -->
      <div class="row unmargin">
        <template v-if="row.items.length > 0">
          <template v-for="(col, colKey) in row.items">
            <!--  -->
            <dt-col
              :key="colKey"
              :row="row"
              :col="col"
              :col-key="colKey"
              :input-view="inputView"
              :client="client"
              :template-items="templateItems"
              :current-selected-row="currentSelectedRow"
              :current-selected-col="currentSelectedCol"
              :current-selected-col-key="currentSelectedColKey"
              :current-selected-item="currentSelectedItem"
              :item-being-dragged="itemBeingDragged"
            ></dt-col>
            <!--  -->
          </template>
        </template>
      </div>
      <!--  -->

      <div class="row text-center p-3 align-items-center" v-if="row.items.length == 0">
        <i class="fad fa-plus fa-10x mb-3"></i>
        <h5>Add item to row - click in to select</h5>
      </div>
    </div>

    <span class="floating-row-delete noprint">
      <button
        class="btn btn-xs btn-light"
        v-tooltip.bottom="'Delete this row & all its items'"
        @click.stop="deleteRow(row)"
      >
        <i class="fad fa-trash"></i>
      </button>
    </span>
  </div>
</template>

<script>
import DtCol from "./DtCol";

export default {
  props: [
    "row",
    "inputView",
    "client",
    "templateItems",
    "currentSelectedRow",
    "currentSelectedColKey",
    "currentSelectedCol",
    "currentSelectedItem",
    "itemBeingDragged",
  ],
  data() {
    return {
      currentSelectedRow: this.currentSelectedRow,
    };
  },
  methods: {
    deleteRow(row) {
      if (
        confirm(
          "Are you sure you wish to remove this row and all its contents? This cannot be undone!?"
        )
      ) {
        var indexToDelete = this.templateItems.findIndex(
          (x) => x.id === row.id
        );
        this.templateItems.splice(indexToDelete, 1);

        this.currentSelectedColKey = null;
        this.currentSelectedCol = null;
        this.currentSelectedItem = null;
        this.currentSelectedRow = null;
        console.log("saveTemplate");
      }
    },
    selectedRow() {
      this.currentSelectedRow = this.row;
      this.currentSelectedColKey = this.currentSelectedColKey
        ? this.currentSelectedColKey
        : 0;
    },
    isTheRowSelected() {
      if (
        this.currentSelectedRow &&
        this.currentSelectedRow.id === this.row.id
      ) {
        return "border-primary";
      }

      return "border-light";
    },
  },
  watch: {
    currentSelectedRow: {
      handler: function (val, oldVal) {
        this.isTheRowSelected();
      },
    },
  },
  components: {
    DtCol,
  },
};
</script>

<style>
.floating-row-delete {
  position: absolute;
  right: -1.15rem;
  top: 0.5rem;
  z-index: 9999;
}
</style>