<script>
import Vue from "vue";
import VPdfBuilder from "@/v-pdf-builder.vue";

export default Vue.extend({
  name: "ServeDev",
  components: {
    VPdfBuilder,
  },
});
</script>

<template>
  <div id="app">
    <v-pdf-builder
      :user="{name: 'alex jay', email: 'alex@amchara.com'}"
      :template="{}"
      :input-view="'Boom'"
      :client="'Boom'"
      :current-selected-item="'Boom'"
      :loaded-template="'Boom'"
      :template-items="'Boom'"
      :current-selected-row="'Boom'"
      :current-selected-col-key="'Boom'"
      :current-selected-col="'Boom'"
      :item-being-dragged="'Boom'"
    />
  </div>
</template>
