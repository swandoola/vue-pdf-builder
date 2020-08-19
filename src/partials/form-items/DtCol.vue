<template>
  <div
    :class="[howManyCols(), isTheColSelected(colKey)]"
    class="border p-2 h-100"
    @click="selectThisCol(colKey, col)"
    style="min-height: 100px;"
  >
    <span
      v-if="this.row.items.length <= 3 && this.row.items.length > 1 && col.items.length == 0"
      style="position: absolute; top: 0rem; right: 0.15rem; z-index: 999;"
    >
      <button class="btn btn-xs btn-light noprint" @click="deleteCol(colKey)">
        <i class="fad fa-trash"></i>
      </button>
    </span>
    <template v-for="(c, cKey) in col.items">
      <ul-list
        v-if="c.type == 'ul'"
        :item="c"
        :key="cKey"
        :col-key="colKey"
        :input-view="inputView"
        :template-items="templateItems"
        :current-selected-row="currentSelectedRow"
        :current-selected-col="currentSelectedCol"
        :current-selected-col-key="currentSelectedColKey"
        :current-selected-item="currentSelectedItem"
        :item-being-dragged="itemBeingDragged"
      ></ul-list>
      <ol-list
        v-if="c.type == 'ol'"
        :item="c"
        :key="cKey"
        :col-key="colKey"
        :input-view="inputView"
        :template-items="templateItems"
        :current-selected-row="currentSelectedRow"
        :current-selected-col="currentSelectedCol"
        :current-selected-col-key="currentSelectedColKey"
        :current-selected-item="currentSelectedItem"
        :item-being-dragged="itemBeingDragged"
      ></ol-list>
      <text-area
        v-if="c.type == 'text'"
        :item="c"
        :key="cKey"
        :col-key="colKey"
        :input-view="inputView"
        :template-items="templateItems"
        :current-selected-row="currentSelectedRow"
        :current-selected-col="currentSelectedCol"
        :current-selected-col-key="currentSelectedColKey"
        :current-selected-item="currentSelectedItem"
        :item-being-dragged="itemBeingDragged"
      ></text-area>

      <text-passage
        v-if="c.type == 'text-passage'"
        :item="c"
        :key="cKey"
        :col-key="colKey"
        :input-view="inputView"
        :template-items="templateItems"
        :current-selected-row="currentSelectedRow"
        :current-selected-col="currentSelectedCol"
        :current-selected-col-key="currentSelectedColKey"
        :current-selected-item="currentSelectedItem"
        :item-being-dragged="itemBeingDragged"
      ></text-passage>
      <heading
        v-if="c.type == 'heading'"
        :item="c"
        :key="cKey"
        :col-key="colKey"
        :input-view="inputView"
        :template-items="templateItems"
        :current-selected-row="currentSelectedRow"
        :current-selected-col="currentSelectedCol"
        :current-selected-col-key="currentSelectedColKey"
        :current-selected-item="currentSelectedItem"
        :item-being-dragged="itemBeingDragged"
      ></heading>
      <clinic-logo
        v-if="c.type == 'logo'"
        :item="c"
        :key="cKey"
        :col-key="colKey"
        :input-view="inputView"
        :template-items="templateItems"
        :current-selected-row="currentSelectedRow"
        :current-selected-col="currentSelectedCol"
        :current-selected-col-key="currentSelectedColKey"
        :current-selected-item="currentSelectedItem"
        :item-being-dragged="itemBeingDragged"
      ></clinic-logo>

      <image-section
        v-if="c.type == 'image'"
        :item="c"
        :key="cKey"
        :col-key="colKey"
        :input-view="inputView"
        :template-items="templateItems"
        :current-selected-row="currentSelectedRow"
        :current-selected-col="currentSelectedCol"
        :current-selected-col-key="currentSelectedColKey"
        :current-selected-item="currentSelectedItem"
        :item-being-dragged="itemBeingDragged"
      ></image-section>
      <practitioner-name
        v-if="c.type == 'practitioner-name'"
        :item="c"
        :key="cKey"
        :col-key="colKey"
        :input-view="inputView"
        :template-items="templateItems"
        :current-selected-row="currentSelectedRow"
        :current-selected-col="currentSelectedCol"
        :current-selected-col-key="currentSelectedColKey"
        :current-selected-item="currentSelectedItem"
        :item-being-dragged="itemBeingDragged"
      ></practitioner-name>
      <client-name
        v-if="c.type == 'client-name'"
        :item="c"
        :key="cKey"
        :col-key="colKey"
        :input-view="inputView"
        :client="client"
        :template-items="templateItems"
        :current-selected-row="currentSelectedRow"
        :current-selected-col="currentSelectedCol"
        :current-selected-col-key="currentSelectedColKey"
        :current-selected-item="currentSelectedItem"
        :item-being-dragged="itemBeingDragged"
      ></client-name>

      <supplements-table
        v-if="c.type == 'supplements-table'"
        :item="c"
        :key="cKey"
        :col-key="colKey"
        :input-view="inputView"
        :client="client"
        :template-items="templateItems"
        :current-selected-row="currentSelectedRow"
        :current-selected-col="currentSelectedCol"
        :current-selected-col-key="currentSelectedColKey"
        :current-selected-item="currentSelectedItem"
        :item-being-dragged="itemBeingDragged"
      ></supplements-table>
    </template>
  </div>
</template>

<script>
import UlList from "./items/UlList";
import OlList from "./items/OlList";
import TextArea from "./items/TextArea";
import TextPassage from "./items/TextPassage";
import ClinicLogo from "./items/ClinicLogo";
import ImageSection from "./items/Image";
import PractitionerName from "./items/PractitionerName";
import ClientName from "./items/ClientName";
import Heading from "./items/Heading";
import SupplementsTable from "./items/SupplementsTable";

export default {
  props: [
    "colKey",
    "col",
    "row",
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
  methods: {
    selectThisItem(item) {
      this.currentSelectedItem = item;
    },
    deleteCol(colKey) {
      if (this.currentSelectedRow) {
        var indexOfRowToRemoveFrom = this.templateItems
          .map(function (x) {
            return x.id;
          })
          .indexOf(this.currentSelectedRow.id);

        var templateItems = this.templateItems;
        var rowToAddTo = templateItems[indexOfRowToRemoveFrom].items;

        if (rowToAddTo.length > 1) {
          rowToAddTo.splice(colKey, 1);
        }

        console.log("saveTemplate");
      }
    },
    selectThisCol(colKey, col) {
      this.currentSelectedCol = col;
      this.currentSelectedColKey = colKey;
      if (this.currentSelectedCol.items.length == 0) {
        this.currentSelectedItem = null;
      }
    },
    checkColSelection(colId) {
      if (this.currentSelectedColKey === colId) {
        if (this.currentSelectedRow) {
          if (this.currentSelectedRow.id === this.row.id) {
            return true;
          }
        }
      }
      return false;
    },
    isTheColSelected(colId) {
      if (this.currentSelectedColKey === colId) {
        if (this.currentSelectedRow) {
          if (this.currentSelectedRow.id === this.row.id) {
            return "border-info";
          }
        }
      }

      return "border-light";
    },
    howManyCols() {
      if (this.row.items.length == 1) {
        return "col-md-12";
      }
      if (this.row.items.length == 2) {
        return "col-md-6";
      }
      if (this.row.items.length == 3) {
        return "col-md-4";
      }
    },
  },
  components: {
    UlList,
    OlList,
    TextArea,
    TextPassage,
    ClinicLogo,
    ImageSection,
    Heading,
    PractitionerName,
    ClientName,
    SupplementsTable,
  },
};
</script>

<style>
</style>