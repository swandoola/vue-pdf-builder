<template>
  <div class="w-100 noprint">
    <div class="card border-0 shadow-none noprint">
      <div class="card-body noprint">
        <ul class="list-group list-group-flush mb-3">
          <div v-for="(item, key) in draggableItems" :key="key">
            <form-builder-item
              :item="item"
              v-if="itemCheck(item)"
              :item-being-dragged="itemBeingDragged"
            ></form-builder-item>
          </div>
        </ul>
        <!--  -->
        <hr />
        <!-- <button
          class="btn btn-sm btn-light btn-block mb-3 noprint"
          @click="clearCurrentSelection"
          v-tooltip:bottom="'Unselect whatever has been selected!'"
        >
          <i class="far fa-times mr-2"></i>Clear Selection
        </button>-->

        <div class="row text-center mt-4">
          <div class="col">
            <button class="btn btn-sm btn-light mb-3 noprint mr-3" @click="printPreview">
              <i class="fas fa-print mr-2"></i>Preview
            </button>

            <button
              class="btn btn-sm btn-light mb-3 noprint"
              @click="saveTemplate"
              v-tooltip:bottom="'Auto-save is enabled - just in case!'"
            >
              <i class="fas fa-check mr-2"></i>Save
            </button>

            <button
              v-if="client"
              class="btn btn-sm btn-primary noprint"
              @click="saveToClientProfile"
              v-tooltip:bottom="'Save this as a PDF to the client profile!'"
              :disabled="busy"
            >
              <i class="fas mr-2" :class="busy ? 'fa-spinner fa-spin' : 'fa-check'"></i>Save to Client Profile
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import FormBuilderItem from "@/partials/item-partials/FormBuilderItem";
export default {
  props: [
    "client",
    "template",
    "user",
    "currentSelectedRow",
    "itemBeingDragged",
  ],
  data() {
    return {
      busy: false,
      draggableItems: [
        {
          id: 1,
          name: "Row",
          type: "row",
          icon: "fa-horizontal-rule",
          items: [
            {
              id: 10,
              name: "Column",
              type: "col",
              icon: "fa-columns",
              items: [],
            },
          ],
        },
        {
          id: 2,
          name: "Column",
          type: "col",
          icon: "fa-columns",
          items: [],
        },

        {
          id: 6,
          name: "Clinic Logo",
          type: "logo",
          icon: "fa-image",
          clinic_name: this.user.clinic.name,
          clinic_logo_url: this.user.clinic.logo,
          options: {
            align: "center",
            header: true,
          },
        },

        {
          id: 10,
          name: "Image",
          type: "image",
          icon: "fa-file-image",
          img_alt: "",
          img_url: "",
          options: {
            align: "center",
          },
        },
        {
          id: 7,
          name: "Heading",
          type: "heading",
          icon: "fa-h1",
          items: [],
          data: [],
          label: "Sample heading",
          options: {
            align: "left",
          },
        },
        {
          id: 5,
          name: "Free Text",
          type: "text",
          icon: "fa-text",
          data: "",
          label: "Label for this text area - can be removed",
          options: {
            align: "left",
          },
        },
        {
          id: 3,
          name: "Bullet List",
          type: "ul",
          icon: "fa-list-ul",
          items: [],
          label: "Label for this list - can be removed",
          data: ["Sample option 1", "Sample option 2", "Sample option 3"],
          options: {
            align: "left",
          },
        },
        {
          id: 4,
          name: "Numbered List",
          type: "ol",
          icon: "fa-list-ol",
          items: [],
          label: "Label for this list - can be removed",
          data: ["Sample option 1", "Sample option 2", "Sample option 3"],
          options: {
            align: "left",
          },
        },

        {
          id: 8,
          name: "Passage of Text",
          type: "text-passage",
          label: "Header for text",
          icon: "fa-align-justify",
          options: {
            align: "left",
          },
        },
        {
          id: 9,
          name: "Supplements Table",
          type: "supplements-table",
          label: "Supplements",
          icon: "fa-table",
          data: [
            {
              sku: "1234",
              title: "Some title here",
              dosage: "Take 6 daily",
              comments: "Some cool commentary in here",
              price: 12.52,
            },
            {
              sku: "56789",
              title: "Ereeeee title here",
              dosage: "Take 92 daily",
              comments: "Some commentary in here",
              price: 150.54,
            },
          ],
          options: {
            align: "left",
          },
        },

        // {
        //   id: 8,
        //   name: this.$store.user.name,
        //   type: "practitioner-name",
        //   icon: "fa-user",
        //   data: this.$store.user.name,
        //   hover: "Will display your name",
        //   options: {
        //     align: "left"
        //   }
        // },
        // {
        //   id: 9,
        //   name: "Client Name",
        //   type: "client-name",
        //   icon: "fa-user",
        //   options: {
        //     align: "left"
        //   }
        // }
      ],
    };
  },
  methods: {
    printPreview() {
      window.print();
    },
    saveTemplate() {
      var silent = false;
      console.log("saveTemplate", silent);
    },
    saveToClientProfile() {
      this.busy = true;
      axios
        .get("/documents/" + this.template.id + "/store-as-pdf")
        .then(({ data }) => {
          console.log("alert", data);
        })
        .catch(() => {
          window.location.href = "/clients/" + this.client.id + "#files-tab";
        })
        .then(() => {
          // console.log("always");
          window.location.href = "/clients/" + this.client.id + "#files-tab";
        });
    },
    itemCheck(item) {
      if (!this.currentSelectedRow) {
        if (item.type == "row") {
          return true;
        }
      } else {
        if (item.type == "row") {
          return true;
        }

        if (item.type == "col") {
          if (this.currentSelectedRow.items.length < 3) {
            return true;
          }
        }

        if (item.type == "ul") {
          return true;
        }
        if (item.type == "ol") {
          return true;
        }
        if (item.type == "text") {
          return true;
        }
        if (item.type == "logo") {
          return true;
        }

        if (item.type == "image") {
          return true;
        }
        if (item.type == "heading") {
          return true;
        }
        if (item.type == "practitioner-name") {
          return true;
        }
        if (item.type == "text-passage") {
          return true;
        }

        if (item.type == "supplements-table") {
          return true;
        }
      }

      return false;
    },
    checkForCols(item) {
      if (item.type == "col" && this.currentSelectedRow.items.length <= 3) {
        return false;
      }
      return true;
    },
    clearCurrentSelection() {
      this.$emit("updateCurrentSelectedRow", null);
      this.$emit("updateCurrentSelectedCol", null);
      this.$emit("updateCurrentSelectedColKey", null);
      this.$emit("updateCurrentSelectedItem", null);
      // this.currentSelectedRow = null;
      // this.currentSelectedColKey = null;
      // this.currentSelectedCol = null;
      // this.currentSelectedItem = null;
    },
  },
  mounted() {
    // this.$on("clearSelectedTemplateItems", () => {
    //   this.clearCurrentSelection;
    // });
  },
  components: {
    FormBuilderItem,
  },
};
</script>

<style>
</style>