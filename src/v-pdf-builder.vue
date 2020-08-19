<template>
  <div class="container-fluid">
    <!--  -->
    <div class="row" v-if="!loadedTemplate">
      <div class="col-md-6 mx-auto">
        <create-template :loaded-template="loadedTemplate" :template-items="templateItems"></create-template>
      </div>
    </div>

    <!--  -->
    <div class="row" :class="inputView ? 'no-gutters' : ''" v-if="loadedTemplate">
      <div class="col-md-9 page-area-print-wide w-100">
        <form-area
          :input-view="inputView"
          :client="client"
          :template-items="templateItems"
          :loaded-template="loadedTemplate"
          :current-selected-row="currentSelectedRow"
          :current-selected-col="currentSelectedCol"
          :current-selected-col-key="currentSelectedColKey"
          :current-selected-item="currentSelectedItem"
          :item-being-dragged="itemBeingDragged"
        ></form-area>
      </div>
      <div class="col-md-3 noprint">
        <div class="sticky-top" style="max-height: 90vh; overflow: scroll">
          <form-builder-options
            class="mb-3"
            v-if="currentSelectedItem"
            :current-selected-row="currentSelectedRow"
            :current-selected-col="currentSelectedCol"
            :current-selected-col-key="currentSelectedColKey"
            :current-selected-item="currentSelectedItem"
            :item-being-dragged="itemBeingDragged"
          ></form-builder-options>
          <form-builder-items
            class="mb-3"
            :user="user"
            :current-selected-row="currentSelectedRow"
            :current-selected-col="currentSelectedCol"
            :current-selected-col-key="currentSelectedColKey"
            :current-selected-item="currentSelectedItem"
            :item-being-dragged="itemBeingDragged"
          ></form-builder-items>
          <how-to></how-to>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import CreateTemplate from "./partials/CreateTemplate";
import FormArea from "./partials/FormArea";
import FormBuilderItems from "./partials/FormBuilderItems";
import FormBuilderOptions from "./partials/FormBuilderOptions";
import HowTo from "./partials/HowTo";

export default {
  name: "VPdfBuilder",
  props: [
    "user",
    "template",
    "inputView",
    "client",

    // Builder specific
    "currentSelectedItem",
    "loadedTemplate",
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
  computed: {
    updateUrl() {
      if (this.inputView) {
        return "/documents/client-version/" + this.loadedTemplate.id;
      }
      return "/documents/" + this.loadedTemplate.id;
    },
  },
  methods: {
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
        this.loadedTemplate = this.template;
        this.templateItems = this.loadedTemplate.data;
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

<style lang="scss" scoped>
.sticky-top {
  top: 1rem;
}
</style>