<template>
  <div>
    <div
      class="custom-control custom-radio"
      v-for="option in options"
      :key="option.key"
    >
      <input
        @click="$emit('select', option.key)"
        type="radio"
        :id="`customRadio${option.key}`"
        name="customRadio"
        class="custom-control-input"
        :checked="option.checked"
      />
      <label class="custom-control-label" :for="`customRadio${option.key}`">{{
        option.label
      }}</label>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    options: { type: Array, required: true },
  },
};
</script>
