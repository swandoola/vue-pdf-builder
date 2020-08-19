<template>
  <div>
    <div class="card border-0 shadow-none">
      <div class="card-body">
        <!-- Label -->
        <template v-if="currentSelectedItem.label || currentSelectedItem.label == ''">
          <div class="row mb-3">
            <div class="col">
              <label for>
                <small>Label</small>
              </label>
              <input
                type="text"
                v-model="currentSelectedItem.label"
                class="form-control form-control-sm"
                @change="updateTemplate"
              />
            </div>
          </div>
        </template>

        <!-- Logo Header -->
        <template
          v-if="currentSelectedItem.options.header || currentSelectedItem.options.header == false"
        >
          <div class="row mb-3">
            <div class="col">
              <label for>
                <small>Show Clinic Name (under logo)</small>
              </label>
              <select
                v-model="currentSelectedItem.options.header"
                class="form-control form-control-sm"
                @change="updateTemplate"
              >
                <option :value="true">Show</option>
                <option :value="false">Hide</option>
              </select>
            </div>
          </div>
        </template>

        <!-- Alignment -->
        <template v-if="currentSelectedItem.options.align">
          <div class="row mb-3">
            <div class="col">
              <label for>
                <small>Alignment</small>
              </label>
              <select
                v-model="currentSelectedItem.options.align"
                class="form-control form-control-sm"
                @change="updateTemplate"
              >
                <option value="left">Left</option>
                <option value="center">Center</option>
                <option value="right">Right</option>
              </select>
            </div>
          </div>
        </template>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: [
    "currentSelectedRow",
    "currentSelectedColKey",
    "currentSelectedCol",
    "currentSelectedItem",
    "itemBeingDragged",
  ],
  data() {
    return {
      templateTitle: "",
    };
  },
  methods: {
    updateTemplate() {
      console.log("saveTemplate");
    },
  },
};
</script>

<style>
</style>