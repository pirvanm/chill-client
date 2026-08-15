<template>
  <div class="container">
    <h1>{{ heading }}</h1>
    <div class="row">
      <div
        class="col-lg-3 col-md-6 mb-4"
        v-for="item in items"
        :key="item.id"
      >
        <div class="category-card">
          <img :src="item.image" />
          <nuxt-link :to="`/playlists/${item.slug}`">
            <div>
              <a href="#category-link" class="category">
                {{ item.name }}
              </a>
            </div>
          </nuxt-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    heading: { type: String, required: true },
    items: { type: Array, default: () => [] },
  },
};
</script>
