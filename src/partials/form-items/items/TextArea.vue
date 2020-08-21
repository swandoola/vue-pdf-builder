<template>
  <div
    class="mb-3 border p-2"
    style="position:relative"
    @click="selectThisItem"
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
    <div :class="'text-' + item.options.align">
      <h6 class="mb-2" v-show="item.label">{{ item.label }}</h6>
      <div class="card border-0 bg-light shadow-none" v-if="!inputView">
        <div class="card-body">
          <p class="mb-0">
            <small>I am just some sample text to show this as a free text area</small>
          </p>
        </div>
      </div>

      <div v-else>
        <div class="row">
          <div class="col">
            <div class="noprint">
              <vue-editor
                v-model="item.data"
                class="bg-white"
                :placeholder="item.label"
                :editor-toolbar="customToolbar"
                @focus="updateTemplate"
              ></vue-editor>
            </div>
            <!-- <textarea
              v-model="item.data"
              id
              rows="6"
              :placeholder="item.label"
              class="form-control noprint"
              
            ></textarea>-->
            <p class="show-when-printing" v-html="item.data"></p>
            <div class="row text-left noprint">
              <div class="col">
                <button
                  v-if="item.data && item.data.length > 3"
                  class="btn btn-xs btn-primary"
                  @click="saveAutoSuggestion"
                  v-tooltip.bottom="'Adds this passage of text to the auto-suggestion library, when you start typing this text next time the passage will be available for you to click and pre-populate.'"
                >
                  <i class="far fa-save mr-2"></i>Save to Auto Suggestions
                </button>
              </div>
            </div>
            <ul class="list-group list-group-flush noprint" v-if="dtas.length > 0">
              <li
                class="list-group-item list-group-item-action"
                v-for="d in dtas"
                :key="d.id"
                @click="selectAutoSuggestion(d)"
              >
                <div v-html="d.content"></div>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { VueEditor } from "vue2-editor";

export default {
  props: [
    "item",
    "colKey",
    "inputView",
    "templateItems",
    "loadedTemplate",
    "currentSelectedRow",
    "currentSelectedColKey",
    "currentSelectedCol",
    "currentSelectedItem",
  ],
  data() {
    return {
      dtas: [],
      customToolbar: [
        [("bold", "italic", "underline")],
        [{ font: [] }, { size: [] }],
        [{ color: [] }, { background: [] }],
      ],
    };
  },
  watch: {
    item: {
      handler(val) {
        if (val.data.length > 2 && val.data.length < 30) {
          this.searchForAutoSuggestions();
        }
      },
      deep: true,
    },
  },
  methods: {
    selectAutoSuggestion(d) {
      this.item.data = d.content;
      this.dtas = [];
      console.log("saveTemplate");
    },
    saveAutoSuggestion() {
      window.axios
        .post("/documents/create-dtas", {
          content: this.item.data,
          type: "text",
        })
        .then(({ data }) => {
          console.log("alert", data);
        });
    },
    searchForAutoSuggestions() {
      console.log("fired");
      if (this.item.data.length > 3) {
        window.axios
          .post("/documents/search-dtas", {
            search: this.item.data,
          })
          .then(({ data }) => {
            this.dtas = data;
          });
      } else {
        this.dtas = [];
      }
    },
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
    updateTemplate() {
      console.log("saveTemplate");
    },
  },
};
</script>

<style>
</style>