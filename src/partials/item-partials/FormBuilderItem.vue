<template>
  <div>
    <li
      class="list-group-item list-group-item-action p-1 border-0"
      style="cursor: move"
      draggable="true"
      @dragstart="dragStart"
      :options="{group: 'menuItems'}"
    >
      <div class="row">
        <div class="col-2 text-center my-auto">
          <i class="fad mr-2" :class="item.icon"></i>
        </div>
        <div class="col my-auto">{{ item.name }}</div>
      </div>
    </li>
    <hr v-if="item.type === 'col'" />
  </div>
</template>

<script>
export default {
  props: ["item"],
  data() {
    return {};
  },
  methods: {
    dragStart(event) {
      this.createNewId();
      this.itemBeingDragged = this.item;
    },
    createNewId() {
      this.item.id = this._uid + new Date().getUTCMilliseconds();
    }
  },
  mounted() {
    this.item.id = this._uid + new Date().getUTCMilliseconds();
  }
};
</script>

<style>
</style>