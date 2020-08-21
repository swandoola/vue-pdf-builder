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
              @removeFromTemplateItems="removeFromTemplateItems"
              @addToTemplateItems="addToTemplateItems"
              @updateLoadedTemplate="updateLoadedTemplate"
              @updateTemplateItems="updateTemplateItems"
              @updateItemBeingDragged="updateItemBeingDragged"
              @updateCurrentSelectedRow="updateCurrentSelectedRow"
              @updateCurrentSelectedCol="updateCurrentSelectedCol"
              @updateCurrentSelectedColKey="updateCurrentSelectedColKey"
              @updateCurrentSelectedItem="updateCurrentSelectedItem"
            ></dt-col>
            <!--  -->
          </template>
        </template>
      </div>
      <!--  -->

      <div class="row text-center p-3 align-items-center" v-if="row.items.length == 0">
        <i class="far fa-plus fa-10x mb-3"></i>
        <h5>Add item to row - click in to select</h5>
      </div>
    </div>

    <span class="floating-row-delete noprint">
      <button
        class="btn btn-xs btn-light"
        v-tooltip.bottom="'Delete this row & all its items'"
        @click.stop="deleteRow(row)"
      >
        <i class="far fa-trash"></i>
      </button>
    </span>
  </div>
</template>

<script>
import DtCol from "@/partials/form-items/DtCol";

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
    return {};
  },
  methods: {
    addToTemplateItems(data) {
      this.$emit("updateTemplateItems", data);
    },
    removeFromTemplateItems(data) {
      this.$emit("removeFromTemplateItems", data);
    },
    updateTemplateItems(data) {
      this.$emit("updateTemplateItems", data);
    },

    updateItemBeingDragged(data) {
      this.$emit("updateItemBeingDragged", data);
    },

    updateCurrentSelectedRow(data) {
      this.$emit("updateCurrentSelectedRow", data);
    },

    updateCurrentSelectedCol(data) {
      this.$emit("updateCurrentSelectedCol", data);
    },

    updateCurrentSelectedColKey(data) {
      this.$emit("updateCurrentSelectedColKey", data);
    },

    updateCurrentSelectedItem(data) {
      this.$emit("updateCurrentSelectedItem", data);
    },
    //
    deleteRow(row) {
      if (
        confirm(
          "Are you sure you wish to remove this row and all its contents? This cannot be undone!?"
        )
      ) {
        var indexToDelete = this.templateItems.findIndex(
          (x) => x.id === row.id
        );
        // this.templateItems.splice(indexToDelete, 1);

        this.$emit("removeFromTemplateItems", indexToDelete);

        // this.currentSelectedColKey = null;
        // this.currentSelectedCol = null;
        // this.currentSelectedItem = null;
        // this.currentSelectedRow = null;

        this.$emit("updateCurrentSelectedRow", null);
        this.$emit("updateCurrentSelectedCol", null);
        this.$emit("updateCurrentSelectedColKey", null);
        this.$emit("updateCurrentSelectedItem", null);

        console.log("saveTemplate");
      }
    },
    selectedRow() {
      this.$emit("updateCurrentSelectedRow", this.row);
      // this.currentSelectedRow = this.row;
      this.$emit(
        "updateCurrentSelectedColKey",
        this.currentSelectedColKey ? this.currentSelectedColKey : 0
      );

      // this.currentSelectedColKey = this.currentSelectedColKey
      //   ? this.currentSelectedColKey
      //   : 0;
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