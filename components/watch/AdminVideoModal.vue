<template>
  <div class="d-flex mb-5">
    <b-modal :id="modalId" title="BootstrapVue" hide-footer>
      <h1>Update Category of this video</h1>
      <select v-model="selectedCategory">
        <option
          v-for="(cat, cindex) in categories"
          :key="`cat-${cindex}`"
          :value="cat.id"
        >
          {{ cat.category_name }}
        </option>
      </select>

      <button
        class="btn btn-sm btn-success"
        @click.prevent="$emit('save-category')"
      >
        Save
      </button>
    </b-modal>

    <button class="btn btn-primary" @click.prevent="$bvModal.show(modalId)">
      Update
    </button>

    <button
      type="button"
      class="btn btn-danger ml-2"
      :class="{ 'btn-sm': small }"
      data-dismiss="modal"
      @click.prevent="$emit('delete')"
    >
      Remove
    </button>

    <button v-if="categoryLabel" type="button" class="btn btn-info ml-2">
      {{ categoryLabel }}
    </button>
  </div>
</template>

<script>
export default {
  props: {
    modalId: { type: String, required: true },
    categories: { type: Array, default: () => [] },
    // The shared { category: N } object the parent reads from when it
    // actually saves -- kept as one object reference (rather than a plain
    // number prop) to preserve the original behavior where every modal on
    // the page writes into the same selection, not an independent one.
    update: { type: Object, required: true },
    categoryLabel: { type: String, default: null },
    small: { type: Boolean, default: false },
  },
  computed: {
    selectedCategory: {
      get() {
        return this.update.category;
      },
      set(value) {
        this.update.category = value;
      },
    },
  },
};
</script>
