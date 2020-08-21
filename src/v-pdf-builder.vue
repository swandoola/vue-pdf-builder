<template>
  <div class="container-fluid">
    <!--  -->
    <div class="row" v-if="!loadedTemplate">
      <div class="col-md-6 mx-auto">
        <create-template
          :template-items="ti"
          :loaded-template="lt"
          @updateLoadedTemplate="updateLoadedTemplate"
          @updateTemplateItems="updateTemplateItems"
        ></create-template>
      </div>
    </div>

    <!--  -->
    <div class="row" :class="inputView ? 'no-gutters' : ''" v-if="loadedTemplate">
      <div class="col-md-9 page-area-print-wide w-100">
        <form-area
          :input-view="inputView"
          :client="c"
          :template-items="ti"
          :loaded-template="lt"
          :current-selected-row="csr"
          :current-selected-col="csc"
          :current-selected-col-key="csck"
          :current-selected-item="csi"
          :item-being-dragged="ibd"
          @addToTemplateItems="addToTemplateItems"
          @updateLoadedTemplate="updateLoadedTemplate"
          @updateTemplateItems="updateTemplateItems"
          @updateItemBeingDragged="updateItemBeingDragged"
          @updateCurrentSelectedRow="updateCurrentSelectedRow"
          @updateCurrentSelectedCol="updateCurrentSelectedCol"
          @updateCurrentSelectedColKey="updateCurrentSelectedColKey"
          @updateCurrentSelectedItem="updateCurrentSelectedItem"
        ></form-area>
      </div>
      <div class="col-md-3 noprint">
        <div class="sticky-top" style="max-height: 90vh; overflow: scroll">
          <form-builder-options
            class="mb-3"
            v-if="csi"
            :current-selected-item="csi"
            @updateCurrentSelectedItem="updateCurrentSelectedItem"
          ></form-builder-options>
          <form-builder-items
            class="mb-3"
            :user="u"
            :current-selected-row="csr"
            :item-being-dragged="ibd"
            @updateItemBeingDragged="updateItemBeingDragged"
            @updateCurrentSelectedRow="updateCurrentSelectedRow"
          ></form-builder-items>
          <how-to></how-to>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import CreateTemplate from "@/partials/CreateTemplate";
import FormArea from "@/partials/FormArea";
import FormBuilderItems from "@/partials/FormBuilderItems";
import FormBuilderOptions from "@/partials/FormBuilderOptions";
import HowTo from "@/partials/HowTo";

export default {
  name: "VPdfBuilder",
  props: [
    "user",
    "template",
    "inputView",
    "client",

    // Builder specific
    "loadedTemplate",
    "templateItems",
    "itemBeingDragged",

    "currentSelectedRow",
    "currentSelectedColKey",
    "currentSelectedCol",
    "currentSelectedItem",
  ],
  data() {
    return {
      csi: this.currentSelectedItem,
      lt: this.loadedTemplate,
      ti: this.templateItems,
      csr: this.currentSelectedRow,
      csc: this.currentSelectedCol,
      csck: this.currentSelectedColKey,
      ibd: this.itemBeingDragged,
      u: this.user,
      c: this.client,
    };
  },
  computed: {
    updateUrl() {
      if (this.inputView) {
        return "/documents/client-version/" + this.loadedTemplate.id;
      }
      return "/documents/" + this.loadedTemplate.id;
    },
  },
  methods: {
    updateUser(data) {
      this.u = data;
      this.$emit("updateUser", this.u);
    },

    updateClient(data) {
      this.c = data;
      this.$emit("updateClient", this.c);
    },

    updateLoadedTemplate(data) {
      this.lt = data;
      this.$emit("updateLoadedTemplate", this.lt);
    },

    addToTemplateItems(data) {
      this.ti.push(data);
      this.$emit("updateTemplateItems", this.ti);
    },

    removeFromTemplateItems(data) {
      this.ti.splice(data, 1);
      this.$emit("updateTemplateItems", this.ti);
    },

    updateTemplateItems(data) {
      this.ti = data;
      this.$emit("updateTemplateItems", this.ti);
    },

    updateItemBeingDragged(data) {
      this.ibd = data;
      this.$emit("updateItemBeingDragged", this.ibd);
    },

    updateCurrentSelectedRow(data) {
      this.csr = data;
      this.$emit("updateCurrentSelectedRow", this.csr);
    },

    updateCurrentSelectedCol(data) {
      this.csc = data;
      this.$emit("updateCurrentSelectedCol", this.csc);
    },

    updateCurrentSelectedColKey(data) {
      this.csck = data;
      this.$emit("updateCurrentSelectedColKey", this.csck);
    },

    updateCurrentSelectedItem(data) {
      this.csi = data;
      this.$emit("updateCurrentSelectedItem", this.csi);
    },

    //
    updateTemplate() {
      window.axios
        .post(this.updateUrl, {
          data: this.templateItems,
        })
        .then(({ data }) => {
          this.loadedTemplate = data.template;
          // console.log("alert", data);
        });
    },
    checkLoaded() {
      if (this.template != null) {
        this.updateLoadedTemplate(this.template);
        this.updateTemplateItems(this.loadedTemplate.data);
        // this.loadedTemplate = this.template;
        // this.templateItems = this.loadedTemplate.data;
      }
    },
  },
  mounted() {
    this.checkLoaded();
    this.$on("saveTemplate", () => {
      this.updateTemplate();
    });
  },
  components: {
    CreateTemplate,
    FormArea,
    FormBuilderItems,
    FormBuilderOptions,
    HowTo,
  },
};
</script>

<style lang="scss" >
@import "./styles/app";

.sticky-top {
  top: 1rem;
}
</style>