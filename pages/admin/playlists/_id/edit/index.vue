<template>
  <div class="container">
    <div class="row">
      <div class="col-md-2">
        <menuAdmin />
      </div>
      <div class="col-md-10">
        <div class="row">
          <div class="col-md-3 text-center border pr-2 mr-2">
            <VideoFilterPanel
              :categories="categories.data"
              :filter="filter"
              :range="range"
              @change="changeSlider"
            />
          </div>

          <div class="col-md-4 border pr-2">
            <h1>
              Edit Playlist / count({{ left.length }})
              <input
                type="text"
                class="form-control"
                placeholder="Enter Playlists Name"
                v-model="form.playlist"
              />
              <input
                type="file"
                placeholder="Choose image"
                @input="pickFile"
                ref="fileInput"
              />
              <img :src="previewImage" alt="" width="100%" />
              <button
                class="btn btn-sm btn-success btn-block mt-1"
                @click.prevent="savePlaylists"
              >
                Save
              </button>
            </h1>
            <client-only>
              <draggable
                class="list-group"
                :list="left"
                group="video"
                style="height: 500px"
              >
                <div
                  class="list-group-item"
                  v-for="(lvideo, ind) in left"
                  :key="ind"
                >
                  {{ lvideo.title }}
                </div>
              </draggable>
            </client-only>
          </div>

          <div class="col-md-4 border ml-2">
            <VideoBrowser
              :videos="videos"
              show-cover-button
              @cover="previewImage = $event"
              @paginate="myCallback"
            />
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
    const playl = await $axios.$get(`/admin/playlist/${params.id}`);

    return {
      videos,
      categories,
      left: playl.data.videos,
      previewImage: playl.data.image,
      playlist: playl.data,
      range: {
        views: [videos.imp.minView, videos.imp.maxView],
        min: videos.imp.minView,
        max: videos.imp.maxView,
      },
      form: {
        playlist: playl.data.name,
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
      previewImage: null,
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
    pickFile() {
      let input = this.$refs.fileInput;
      let file = input.files;
      if (file && file[0]) {
        let reader = new FileReader();
        reader.onload = (e) => {
          this.previewImage = e.target.result;
        };
        reader.readAsDataURL(file[0]);
      }
    },
    savePlaylists() {
      this.$axios
        .post(`/admin/playlist/${this.playlist.id}/edit`, {
          playlist: this.form.playlist,
          videos: this.left,
          image: this.previewImage,
        })
        .then((response) => {
          if (response.status == 200) {
            Swal.fire({
              title: "Playlist updated success",
              icon: "success",
              showConfirmButton: true,
              timer: 1500,
              confirmButtonText: "Ok",
            }).then((result) => {
              if (result.isConfirmed) {
                this.$router.push("/admin/playlists");
              }
            });
          }
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
.container {
  margin-top: 50px;
}
</style>
