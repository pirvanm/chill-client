<template>
  <div class="container page-playlist">
    <div class="row">
      <div class="col-md-2 left-menu">
        <menuAdmin />
      </div>
      <div class="col-md-10 dinamic-playlist">
        <div class="row">
          <div class="col-md-3 border ml-3 mr-2 bg-light rounded">
            <VideoFilterPanel
              :categories="categories.data"
              :filter="filter"
              :range="range"
              @change="changeSlider"
            />
          </div>

          <div class="col-md-4 border pr-2 bg-light rounded">
            <h1>New Playlist</h1>
            <h3>count({{ left.length }})</h3>
            <input
              type="text"
              class="form-control"
              placeholder="Enter Playlists Name"
              v-model="form.playlist"
            />
            <button
              class="btn btn-sm btn-success btn-block mt-1"
              @click.prevent="savePlaylists"
            >
              Save
            </button>

            <client-only>
              <draggable
                class="list-group"
                :list="left"
                group="video"
                style="height: 500px"
              >
                <div
                  class="list-group-item mb-2"
                  v-for="lvideo in left"
                  :key="lvideo.id"
                >
                  {{ lvideo.title }}
                </div>
              </draggable>
            </client-only>
          </div>

          <div class="col-md-4 border ml-2 bg-light rounded">
            <VideoBrowser :videos="videos" @paginate="myCallback" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Swal from "sweetalert2";
import menuAdmin from "@/components/MenuAdmin";
import VideoFilterPanel from "@/components/admin/VideoFilterPanel";
import VideoBrowser from "@/components/admin/VideoBrowser";
export default {
  async asyncData({ query, params, error, $axios }) {
    const videos = await $axios.$get(`/admin/videos`);
    const categories = await $axios.$get(`/admin/categories`);
    return {
      videos,
      categories,
      range: {
        views: [videos.imp.minView, videos.imp.maxView],
        min: videos.imp.minView,
        max: videos.imp.maxView,
      },
      form: {
        playlist: "",
      },
    };
  },
  middleware: ["admin"],
  components: {
    menuAdmin,
    VideoFilterPanel,
    VideoBrowser,
  },
  data() {
    return {
      timer: null,
      left: [],
      filter: {
        category: "",
        duration: 1,
        title: "",
        tag: "",
      },
    };
  },
  watch: {
    filter: {
      // This will let Vue know to look inside the array
      deep: true,

      // We have to move our method to a handler field
      handler() {
        clearTimeout(this.timer);
        this.timer = setTimeout(() => {
          this.filterVideo();
        }, 500);
      },
    },
  },
  methods: {
    savePlaylists() {
      this.$axios
        .post("/admin/playlist/add", {
          playlist: this.form.playlist,
          videos: this.left,
        })
        .then((response) => {
          Swal.fire({
            icon: "success",
            title: "Playlist created success",
            showConfirmButton: false,
            timer: 1500,
          });
          this.left = [];
          this.form.playlist = "";
        });
    },
    myCallback() {
      this.$axios
        .get(
          `/admin/videos?page=${this.videos.meta.current_page}&category=${this.filter.category}&duration=${this.filter.duration}&min=${this.range.views[0]}&max=${this.range.views[1]}&title=${this.filter.title}&tag=${this.filter.tag}`
        )
        .then((response) => {
          this.videos.data = response.data.data;
          this.videos.meta = response.data.meta;
        });
    },
    filterVideo() {
      this.$axios
        .get(
          `/admin/videos?category=${this.filter.category}&duration=${this.filter.duration}&min=${this.range.views[0]}&max=${this.range.views[1]}&title=${this.filter.title}&tag=${this.filter.tag}`
        )
        .then((response) => {
          this.videos.data = response.data.data;
          this.videos.meta = response.data.meta;
        });
    },
    changeSlider() {
      clearTimeout(this.timer);
      this.timer = setTimeout(() => {
        this.filterVideo();
      }, 500);
    },
  },
};
</script>
<style scoped>
@media (min-width: 1200px) {
  .container,
  .container-sm,
  .container-md,
  .container-lg,
  .container-xl {
    max-width: 2000px;
  }
}
.container {
  margin-top: 50px;
}
</style>
