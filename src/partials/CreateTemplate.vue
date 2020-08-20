<template>
  <div>
    <div class="card border-0 shadow-sm">
      <div class="card-body">
        <div class="row">
          <div class="col">
            <label for>Health Report Template Title</label>
            <input
              type="text"
              v-model="title"
              class="form-control"
              placeholder="An amazing title..."
            />
            <button class="btn btn-primary mt-4 float-right" @click="createTemplate">
              <i class="far fa-arrow-right mr-2"></i>Next
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["loadedTemplate", "templateItems"],
  data() {
    return {
      title: "",
    };
  },
  methods: {
    createTemplate() {
      window.axios
        .post("/documents", {
          title: this.title,
        })
        .then(({ data }) => {
          this.loadedTemplate = data.template;
          this.templateItems = data.template.data;
          console.log("alert", data);
        });
    },
  },
};
</script>

<style>
</style>