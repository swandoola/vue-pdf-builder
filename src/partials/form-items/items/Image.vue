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
      <button class="btn btn-xs btn-light noprint" @click="deleteItemFromCol(item)">
        <i class="far fa-trash"></i>
      </button>
    </span>
    <div :class="'text-' + item.options.align">
      <img :src="item.img_url" :alt="item.img_alt" width="100%" />
      <vue-dropzone
        class="noprint"
        v-show="!item.img_url"
        ref="myVueDropzone"
        id="dropzone"
        :options="dropzoneOptions"
        @vdropzone-complete="uploaded"
      ></vue-dropzone>
    </div>
  </div>
</template>

<script>
import vue2Dropzone from "vue2-dropzone";
import "vue2-dropzone/dist/vue2Dropzone.min.css";

export default {
  props: [
    "item",
    "colKey",
    "templateItems",
    "loadedTemplate",
    "currentSelectedRow",
    "currentSelectedColKey",
    "currentSelectedCol",
    "currentSelectedItem",
  ],
  data() {
    return {
      dropzoneOptions: {
        url: "/documents/" + this.loadedTemplate.id + "/upload-image",
        thumbnailWidth: 150,
        maxFilesize: 24,
        headers: {
          "x-csrf-token": document
            .querySelectorAll("meta[name=csrf-token]")[0]
            .getAttributeNode("content").value,
        },
        dictDefaultMessage:
          "<i class='far fa-upload fa-4x text-primary'></i><br><br>Click here to select a file from your computer or drag n' drop a file to begin uploading",
      },
    };
  },
  methods: {
    uploaded(res) {
      var url = JSON.parse(res.xhr.response).url;
      this.item.img_url = url;
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
  components: {
    vueDropzone: vue2Dropzone,
  },
};
</script>

<style>
</style>