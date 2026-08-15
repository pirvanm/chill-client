<template>
  <div>
    <h1>List of posible Songs / count({{ videos.meta.total }})</h1>
    <hr />
    <client-only>
      <draggable class="list-group" :list="videos.data" group="video">
        <div
          class="list-group-item mb-2"
          v-for="video in videos.data"
          :key="video.title"
        >
          Title: {{ video.title }} Views: {{ video.views }} Duration
          {{ video.duration }} Type: {{ video.type_duration }}
          <div>
            <button
              v-if="showCoverButton"
              @click.prevent="$emit('cover', video.thumbnail)"
            >
              Cover Me
            </button>
            <br v-if="showCoverButton" />
            See Video

            <button class="btn btn-sm btn-info btn-block mt-1">
              <a :href="'/watch/' + video.videoId" target="_blank"
                >See Video with id : {{ video.videoId }}</a
              >
              <a :href="video.thumbnail" target="_blank"
                >See Video Image: {{ video.videoId }}</a
              >
            </button>
          </div>
        </div>
      </draggable>
    </client-only>

    <client-only placeholder="Loading...">
      <pagination
        v-model="videos.meta.current_page"
        :records="videos.meta.total"
        @paginate="$emit('paginate')"
        :per-page="videos.meta.per_page"
        :chunk="3"
        chunksNavigation="fixed"
      />
    </client-only>
  </div>
</template>

<script>
export default {
  props: {
    videos: { type: Object, required: true },
    showCoverButton: { type: Boolean, default: false },
  },
};
</script>
