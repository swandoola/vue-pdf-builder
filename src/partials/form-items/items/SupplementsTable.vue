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
    <div class="supplement-table-printing" :class="'text-' + item.options.align">
      <h3 class="mb-2" v-show="item.label">{{ item.label }}</h3>
      <div class="card border-0 bg-light shadow-none" v-if="!inputView">
        <div class="card-body">
          <table class="health-report table">
            <thead>
              <tr>
                <th scope="col" width="5%">SKU</th>
                <th scope="col" width="10%">Title</th>
                <th scope="col" width="10%">Dosage</th>
                <th scope="col" width="20%">Comments</th>
                <th scope="col" width="10%">Price</th>
              </tr>
            </thead>
            <tbody>
              <tr>
                <td>1234</td>
                <td>Supp Title</td>
                <td>Take x per x</td>
                <td>Comments and extra info</td>
                <td>£12.50</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

      <div v-else>
        <div class="row">
          <div class="col">
            <table class="health-report table">
              <thead>
                <tr>
                  <th scope="col">SKU</th>
                  <th scope="col">Title</th>
                  <th scope="col">Dosage</th>
                  <th scope="col">Comments</th>
                  <th scope="col">Price</th>
                  <th scope="col" class="noprint"></th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(i, key) in item.data" :key="key">
                  <td>{{ i.sku }}</td>
                  <td>{{ i.title }}</td>
                  <td>{{ i.dosage }}</td>
                  <td>{{ i.comments }}</td>
                  <td>£{{ i.price }}</td>
                  <td class="noprint">
                    <button class="btn btn-xs btn-light" @click="deleteTableRow(i)">
                      <i class="far fa-minus"></i>
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>

            <!-- Add new supplement -->
            <div class="noprint">
              <input
                type="text"
                @input="findSupplement"
                v-model="search"
                class="form-control form-control-sm mb-3"
                placeholder="Supplement search..."
              />
              <ul class="list-group" v-if="supplements.length > 0">
                <li
                  class="list-group-item list-group-item-action"
                  v-for="s in supplements"
                  @click="selectSupplement(s)"
                  :key="s.id"
                >{{ s.title }}</li>
              </ul>
            </div>
          </div>
        </div>
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
      search: "",
      supplements: [],
    };
  },
  methods: {
    deleteTableRow(item) {
      var indexOf = this.item.data.findIndex((x) => x.id == item.id);
      this.item.data.splice(indexOf, 1);
      console.log("saveTemplate");
    },
    selectSupplement(supplement) {
      this.item.data.push(supplement);
      this.search = "";
      this.supplements = [];
      console.log("saveTemplate");
    },
    findSupplement() {
      if (this.search.length > 2) {
        window.axios
          .post("/supplements/search", {
            search: this.search,
          })
          .then(({ data }) => {
            this.supplements = data;
          });
      } else {
        this.supplements = [];
      }
    },

    selectAutoSuggestion(d) {
      this.item.data = d.content;
      this.dtas = [];
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
.floating-remove-table-row {
  position: absolute;
  right: 0.25rem;
  top: 0rem;
}
</style>