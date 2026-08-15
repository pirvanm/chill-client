<template>
  <div>
    <h1>Filters</h1>

    <div class="form-group col-md-12 pr-2 mr-2">
      <h1 for="inputState">#1 Filter Pick a Category</h1>
      <select id="inputState" class="form-control" v-model="filter.category">
        <option selected value="">Choose...</option>

        <option
          v-for="cat in categories"
          :key="cat.id"
          :value="cat.category_name"
        >
          {{ cat.category_name }}
        </option>
      </select>
    </div>
    <label for="inputState">#2 Pick Duration</label>
    <br /><br />
    <div
      class="form-check form-check-inline"
      v-for="n in [1, 2, 3, 4, 5]"
      :key="n"
    >
      <input
        class="form-check-input"
        type="radio"
        name="inlineRadioOptions"
        :id="`inlineRadio${n}`"
        :value="n"
        v-model="filter.duration"
      />
      <label class="form-check-label" :for="`inlineRadio${n}`">{{ n }}</label>
      <br /><br />
    </div>

    <form>
      <div class="form-group">
        <label for="inputState">#3 Chouse Number of Views</label>
        <client-only>
          <vue-slider
            v-model="range.views"
            :min="range.min"
            :max="range.max"
            @change="$emit('change')"
          ></vue-slider>
        </client-only>
        <div class="row">
          <input
            class="col-md-6 form-control pb-2"
            type="number"
            v-model="range.views[0]"
            @input="$emit('change')"
          />
          <br /><br />
          <input
            class="col-md-6 form-control"
            type="number"
            v-model="range.views[1]"
            @input="$emit('change')"
          />
        </div>
      </div>

      <div class="form-group">
        <label for="inputState">#4 Type a Title</label>

        <input
          type="text"
          class="form-control"
          id="title"
          v-model="filter.title"
        />
      </div>

      <div class="form-group">
        <label for="inputState">#5 Type a Tagg</label>

        <input
          type="text"
          class="form-control"
          id="tagg"
          v-model="filter.tag"
        />
      </div>
    </form>
  </div>
</template>

<script>
export default {
  props: {
    categories: { type: Array, default: () => [] },
    // Both mutated in place by this component -- same object reference as
    // the parent's, which already has a deep watcher on `filter` and reacts
    // to these mutations without needing an event round-trip.
    filter: { type: Object, required: true },
    range: { type: Object, required: true },
  },
};
</script>
