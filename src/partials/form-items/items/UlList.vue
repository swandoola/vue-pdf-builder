<template>
  <div
    class="mb-3 border p-3"
    style="position:relative"
    @click="selectThisItem"
    :class="isTheItemSelected(item.id)"
  >
    <span
      style="position: absolute; bottom: 0rem; right: .15rem; z-index: 999;"
      v-tooltip.bottom="'Delete this item'"
    >
      <button class="btn btn-xs btn-light noprint" @click.stop="deleteItemFromCol(item)">
        <i class="far fa-trash"></i>
      </button>
    </span>
    <div :class="'text-' + item.options.align" class="list-print">
      <h5 class="mb-2" v-show="item.label">{{ item.label }}</h5>
      <ul class="ml-4" v-if="!inputView">
        <li>Sample list item 1</li>
        <li>Sample list item 2</li>
        <li>Sample list item 3</li>
        <li>Sample list item 4</li>
      </ul>

      <div v-if="inputView">
        <ul class="ml-4 noprint">
          <li v-for="(i, key) in item.data" :key="key" class="mb-3">
            <div class="row">
              <div class="col">
                <input
                  :key="key"
                  type="text"
                  v-model="item.data[key]"
                  class="form-control form-control-sm"
                />
              </div>

              <div class="col-auto">
                <button class="btn btn-xs btn-light noprint" @click="removeItemFromList(key)">
                  <i class="far fa-times"></i>
                </button>
              </div>
            </div>
          </li>
        </ul>

        <ul class="ml-4 show-when-printing">
          <li v-for="(i, key) in item.data" :key="'hidden-' + key">{{i}}</li>
        </ul>

        <form @submit.prevent="addItemToList" class="noprint">
          <input
            type="text"
            v-model="newListItem"
            class="form-control form-control-sm mt-3"
            placeholder="New Item..."
            @input="searchForAutoSuggestions"
          />
        </form>

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
</template>

<script>
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
      newListItem: "",
    };
  },
  watch: {
    item: {
      handler: function (val, oldVal) {
        console.log("firing");
        console.log("saveTemplate");
      },
      deep: true,
    },
  },
  methods: {
    selectAutoSuggestion(d) {
      this.item.data.push(d.content);
      this.dtas = [];
      this.newListItem = "";
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
      console.log("searching");
      if (this.newListItem.length > 3) {
        window.axios
          .post("/documents/search-dtas", {
            search: this.newListItem,
            type: "list",
          })
          .then(({ data }) => {
            this.dtas = data;
          });
      } else {
        this.dtas = [];
      }
    },
    addItemToList() {
      this.item.data.push(this.newListItem);
      this.newListItem = "";
      console.log("saveTemplate");
    },
    removeItemFromList(index) {
      this.item.data.splice(index, 1);
      console.log("saveTemplate");
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
  },
};
</script>

<style>
</style>