<template>
  <div class="body">
    <div class="main">
      <div class="heading">
        <button type="submit" v-on:click="toggleSidebar()">
          <i class="fas fa-bars"></i>
        </button>
        <span class="title">Chillwhispers</span>
      </div>
      <newLeftBar />
      <div class="content">
        <div>
          <search />
        </div>

        <!-- <div class="clearfix"></div> -->

        <div class="container">
          <div v-if="vids.length">
            <div class="row">
              <div class="col-md-8">
                <div class="breadcrumbs">
                  <a href="/">Home</a>/
                  <nuxt-link
                    :to="routeToLang(`/videos/${vid.category.category_name}`)"
                  >
                    {{ vid.category.category_name }}
                  </nuxt-link>
                </div>

                <AdminVideoModal
                  v-if="isAdmin"
                  modal-id="modal-primary"
                  :categories="categories"
                  :update="update"
                  small
                  @save-category="saveCategory(vid.id, 'modal-primary')"
                  @delete="deleteVideo(vid.id, 'watch', 0)"
                />

                <h3 class="title mt-3 pt-2 pb-3">{{ vid.title }}</h3>

                <div class="wrapper embed-responsive embed-responsive-4by3">
                  <!-- <client-only> -->
                  <youtube
                    ref="youtube"
                    width="560"
                    height="315"
                    :video-id="vid.videoId"
                    :player-vars="playerVars"
                    @ended="endVideo"
                    class="player embed-responsive-item"
                  >
                  </youtube>
                  <!-- </client-only>
                   -->
                </div>

                <div class="mt-3 text-center mb-3">
                  <span @click="play">
                    <span v-show="isPlay" class="pink">
                      <i :class="`fas fa-play fa-2x `"></i>
                    </span>
                    <span v-show="!isPlay">
                      <i :class="`fas fa-pause fa-2x`"></i>
                    </span>
                  </span>

                  <span @click="nextVideo" class="cursor">
                    <i class="fas fa-forward fa-2x"></i>
                  </span>

                  <span @click="triggerLoop" :class="loop ? 'pink' : 'white'">
                    <i class="fas fa-redo-alt fa-2x"></i>
                  </span>
                </div>

                <div></div>

                <div class="video-desc" v-html="vid.description"></div>
              </div>
              <div class="col-md-4">
                <h1>Coming up</h1>
                <div
                  class="video-card mb-4"
                  v-for="(v, index) in vids"
                  :key="`${index}v`"
                  @click.prevent="gotoWatch(v.videoId)"
                >
                  <AdminVideoModal
                    v-if="isAdmin"
                    :modal-id="`modal-${index}v`"
                    :categories="categories"
                    :update="update"
                    :category-label="v.category.category_name"
                    @save-category="saveCategory(v.id, `modal-${index}v`)"
                    @delete="deleteVideo(v.id, 'category', index)"
                  />
                  <img :src="v.thumbnail" />
                  <p class="title">{{ v.title }}</p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import newLeftBar from "@/components/newLeftBar";
import search from "@/components/Search";
import AdminVideoModal from "@/components/watch/AdminVideoModal";
export default {
  components: {
    newLeftBar,
    search,
    AdminVideoModal,
  },
  props: ["vid", "vids", "categories"],
  data() {
    return {
      innerWidth: 0,
      loop: false,
      isPlay: true,
      isAdmin: false,
      playerVars: {
        autoplay: 1,
        modestbranding: 0,
        showinfo: 0,
        controls: 1,
      },
      url: "",
      update: {
        category: 1,
      },
    };
  },

  computed: {
    player() {
      return this.$refs.youtube.player;
    },
    query() {
      return this.$route.query.v;
    },
  },
  watch: {
    query: {
      deep: true,

      handler() {
        this.gotoWatch(this.$route.query.v);
      },
    },
  },

  created() {
    this.url = window.location.href;
    this.addToHistory();
    this.checkAdmin();
    if (process.browser) {
      window.addEventListener("resize", this.handleResize);
      this.handleResize();
    }
  },
  destroyed() {
    window.removeEventListener("resize", this.handleResize);
  },
  methods: {
    routeToLang(loc) {
      if (this.$i18n.locale == "en") {
        return loc;
      } else {
        return "/" + this.$i18n.locale + loc;
      }
    },
    handleResize() {
      this.innerWidth = window.innerWidth;
    },
    triggerLoop() {
      this.loop = !this.loop;
    },
    checkAdmin() {
      if (this.$auth.loggedIn) {
        this.isAdmin = this.$auth.user.isAdmin;
      }
    },
    play() {
      this.isPlay = !this.isPlay;
      if (this.isPlay) {
        this.player.playVideo();
      } else {
        this.player.pauseVideo();
      }
    },
    pause() {
      this.player.pauseVideo();
    },
    endVideo() {
      this.$emit("endVideo", this.loop);
    },
    nextVideo() {
      this.$router.push(this.routeToLang(`/watch?v=${this.vids[0].videoId}`));
      this.player.playVideo();
    },
    saveCategory(video, modal) {
      this.$axios
        .post("save-category-to-video", {
          vid: video,
          category: this.update.category,
        })
        .then((response) => {
          this.$bvModal.hide(modal);
        });
    },
    deleteVideo(id, watch, index) {
      this.$axios.delete(`/delete-video/${id}`).then((response) => {
        if (watch === "watch") {
          this.nextVideo();
        } else {
          this.vids.splice(index, 1);
        }
      });
    },
    gotoWatch(v) {
      window.scrollTo(0, 0);
      this.$router.push(this.routeToLang(`/watch?v=${v}`));
      this.$axios.get(`/watch/${v}`).then((response) => {
        this.$emit("respo", response);
      });
      this.addToHistory();
    },
    toggleSidebar() {
      const sidebar = document.querySelector(".sidebar");
      sidebar.classList.toggle("shown");
    },
    addToHistory() {
      var data = this.vid;
      this.$store.dispatch("history/addVideoToHistory", data);
    },
  },
};
</script>
<style scoped>
.c {
  padding-left: 3%;
  background: url("~assets/background.png");
  height: 100%;
  background-repeat: no-repeat; /* Do not repeat the image */
  background-size: cover; /* Resize the background image to cover the entire container */
}

.col-md-5 {
  color: green;
}

.card {
  background-color: transparent;
  background-color: none;
  border: none;
}
.card-body h4,
.card-body p {
  font-size: bold;
  color: #dadada;
}
.card-body p {
  color: purple;
}

.card img {
  border-radius: 40px;
}

p {
  color: red;
}

/*  YT BAR */

.ytp-chrome-bottom.ytp-volume-slider-active {
  display: none;
}

.next-list {
  background-color: #482139;
  border-top-left-radius: 50px;
}
.next-list h1 {
  padding-top: 20px;
}
.badge-primary {
  color: #fff;
  background-color: #007bff;
  padding: 10px;
  margin-right: 10px;
  margin-bottom: 15px;
  font-size: 1.2em;
}
.video-desc {
  color: white;
  margin-left: 50px;
}

.title {
  font-size: 1em;
  color: white;
  font-weight: bold;
}

@media (min-width: 320px) {
  title {
    font-size: 0.5em;
  }
}

.leftBar {
  background-color: #090909;
  position: fixed;
  margin-right: 100px;
  color: #8422a6;
  border-top-right-radius: 50px;
  opacity: 0.8;
  width: 300px;
}

@media (min-device-width: 320px) and (max-device-width: 768px) {
  .video-desc {
    display: none;
  }

  h3 title {
    font-size: 1em !important;
  }

  title {
    font-size: 1em !important;
  }
}

.ytp-chrome-controls {
  color: grey;
  display: none;
}

.ytp-play-button.ytp-button {
  color: red;
}
.white {
  cursor: pointer;
  color: white !important;
}

.pink {
  cursor: pointer;
  color: #d303fc;
}
.cursor {
  cursor: pointer;
}
.selected-border {
  border: 5px solid purple !important;
}
.fa-2x {
  padding-right: 10px;
  margin-right: 10px;
}

.list-inline {
  margin-left: 100px;
  padding-left: 100px;
}
.list-inline li {
  padding-right: 20px;
}

.mobile-player {
  position: relative;
  display: block;
  width: 100%; /* width of iframe wrapper */
  height: auto;
  margin: auto;
  padding: 0% 0% 1.25%;
  overflow: hidden;
}
.mobile-player iframe {
  top: 0;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 246px;
}

.wrapper {
  position: relative;
  width: 100%;
}

iframe {
  position: absolute;
  width: 100%;
  height: 100% !important;
  background: #000;
}

.iframe-container {
  position: relative;
  width: 100%;

  height: 0;
}
.iframe-container iframe {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
.player.embed-responsive-item {
  height: 200px;
}

.fa-2x {
  border: 5px solid white;
  padding: 11px;
  height: 55px;
  width: 55px;
  border-radius: 38px;
}
</style>
