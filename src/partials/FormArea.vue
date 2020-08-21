<template>
  <div>
    <div class="card border-0 shadow-none">
      <!-- <div class="card-header noprint">
        <div class="row">
          <div class="col my-auto" v-if="!inputView">
            <h6 class="mb-0">Document template builder</h6>
            <span>
              <small>To get started, drag a row from the right into the left, click into it and add some items!</small>
            </span>
          </div>
          <div class="col my-auto" v-else>
            <button @click="closeLoadedTemplate" class="btn btn-sm btn-light mr-3">
              <i class="far fa-times"></i>
            </button>
            <a
              :href="'/documents/' + $store.loadedTemplate.id + '/preview-as-pdf'"
              class="btn btn-sm btn-light mr-3"
              target="_blank"
            >
              <i class="far fa-file-pdf mr-2"></i>Preview PDF
            </a>

            <button
              @click="storePdf"
              class="btn btn-sm btn-light"
              v-tooltip.bottom="'Makes the PDF available in the client portal'"
            >
              <i
                class="far mr-2"
                :class="storeBusy ? 'fa-spinner fa-spin' : 'fa-file-pdf'"
                :disabled="storeBusy"
              ></i>Store in client files
            </button>
          </div>
          <div class="col-auto my-auto ml-auto">
            <button
              class="btn btn-sm btn-light"
              @click="clearCurrentSelection"
              v-tooltip:bottom="'Selects the root page element. Does not clear document content.'"
            >
              <i class="far fa-times mr-2"></i>Reset Selection
            </button>
          </div>
        </div>
      </div>-->
      <div
        class="card-body bg-dark"
        @click="clearCurrentSelection"
        @dragover.prevent
        @drop="dropItem"
        :options="{group: 'menuItems'}"
      >
        <!--  -->

        <div class="A4">
          <div class="row unmargin" v-if="templateItems.length > 0">
            <div class="col unmargin">
              <draggable
                :list="templateItems"
                element="ul"
                class="list-group list-group-flush reordering unmargin"
                @start="doThis"
                @end="doThat"
                :options="{group: 'actualItems'}"
              >
                <li
                  class="list-group-item border-0 unmargin"
                  v-for="item in templateItems"
                  :key="item.id"
                >
                  <!-- <div > -->
                  <dt-row
                    v-if="item && item.type == 'row'"
                    :row="item"
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
                  ></dt-row>
                  <!-- </div> -->
                </li>
              </draggable>
            </div>
          </div>
          <div class="row my-5 text-center" v-if="templateItems.length == 0">
            <div class="col">
              <i class="far fa-info-circle fa-10x mb-3 text-primary"></i>
              <p>To begin building your health report, drag a row on to the page.</p>
              <p>Add an item from the right into your row, such as a clinic logo or a passage of text</p>
              <p>Add one item per row, or add columns to rows to enhance the layout of your health report</p>
              <p>Rows may be re-ordered by dragging them up and down the page</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import DtRow from "@/partials/form-items/DtRow";
import draggable from "vuedraggable";
export default {
  props: [
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
  data() {
    return {
      items: [],
      storeBusy: false,
    };
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
    closeLoadedTemplate() {
      console.log("closeLoadedTemplate");
      this.clearCurrentSelection();
    },
    storePdf() {
      console.log("saveTemplate");
      this.storeBusy = true;
      var url = "/documents/" + this.loadedTemplate.id + "/store-as-pdf";

      window.axios.post(url).then(({ data }) => {
        console.log("alert", data);
        this.storeBusy = false;
      });
    },
    draggedOver(i) {
      console.log(i);
    },
    doThis(i) {},
    doThat() {
      this.updateTemplate();
    },
    updateTemplate() {
      console.log("saveTemplate");
    },
    clearCurrentSelection() {
      console.log("saveTemplate");
      this.$emit("updateCurrentSelectedRow", null);
      this.$emit("updateCurrentSelectedCol", null);
      this.$emit("updateCurrentSelectedColKey", null);
      this.$emit("updateCurrentSelectedItem", null);
      // this.currentSelectedRow = null;
      // this.currentSelectedColKey = null;
      // this.currentSelectedCol = null;
      // this.currentSelectedItem = null;
    },
    dropItem(event) {
      if (this.itemBeingDragged) {
        var dragFromItems = this.$parent.draggableItems;

        var itemToBeAdded = _.cloneDeep(this.itemBeingDragged);

        if (itemToBeAdded.type == "row") {
          // this.currentSelectedColKey = null;
          // this.currentSelectedCol = null;
          // this.currentSelectedItem = null;
          // this.currentSelectedRow = null;
          this.$emit("updateCurrentSelectedRow", null);
          this.$emit("updateCurrentSelectedCol", null);
          this.$emit("updateCurrentSelectedColKey", null);
          this.$emit("updateCurrentSelectedItem", null);
        }

        if (this.currentSelectedRow) {
          // We should check if there are other cols in here
          // Default has 1 column
          var indexOfRowToAddTo = this.templateItems
            .map(function (x) {
              return x.id;
            })
            .indexOf(this.currentSelectedRow.id);

          var templateItems = this.templateItems;
          var rowToAddTo = templateItems[indexOfRowToAddTo].items;
          // If the row contains a col & the incoming item is not a col add it to the col
          if (
            rowToAddTo[this.currentSelectedColKey].type == "col" &&
            itemToBeAdded.type != "col"
          ) {
            rowToAddTo[this.currentSelectedColKey].items.push(itemToBeAdded);
          } else if (itemToBeAdded.type == "col") {
            if (rowToAddTo.some((e) => e.id === itemToBeAdded.id)) {
              itemToBeAdded.id = new Date().getUTCMilliseconds();
            }

            // Add a col to the row
            // Check if the row contains 3 or less cols
            if (rowToAddTo.length == 1 || rowToAddTo.length == 2) {
              var index = rowToAddTo.push(itemToBeAdded) - 1;

              this.$emit("updateCurrentSelectedCol", itemToBeAdded);
              this.$emit("updateCurrentSelectedColKey", index);
              // this.currentSelectedCol = itemToBeAdded;
              // this.currentSelectedColKey = index;
            }
          }
        } else {
          // If its just a row, add it
          this.$emit("addToTemplateItems", itemToBeAdded);
          // this.templateItems.push(itemToBeAdded);
          this.$emit("updateCurrentSelectedRow", itemToBeAdded);
          // this.currentSelectedRow = itemToBeAdded;
          this.$emit("updateCurrentSelectedCol", itemToBeAdded.items[0]);
          // this.currentSelectedCol = itemToBeAdded.items[0];
          this.$emit("updateCurrentSelectedColKey", 0);
          // this.currentSelectedColKey = 0;

          this.scrollToRow(itemToBeAdded.id);
        }

        // Ready up for the next one
        this.$emit("updateItemBeingDragged", null);
        this.itemBeingDragged = null;
        this.updateTemplate();
      }
      this.updateTemplate();
    },
    scrollToRow(id) {
      console.log(id);
      this.$nextTick(() => {
        this.$scrollTo("#row-" + id, 600);
      });

      // document.getElementById("row" + id).scrollIntoView();
    },
  },
  mounted() {},
  components: {
    DtRow,
    draggable,
  },
};
</script>

<style>
</style>