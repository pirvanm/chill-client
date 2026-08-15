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

        <div class="clearfix"></div>
        <div class="container">
          <div v-if="videos.length">
            <div class="row">
              <div class="col-md-8">
                <h1 class="mb-2">{{ videos.title }}</h1>

                <div class="wrapper embed-responsive embed-responsive-4by3">
                  <youtube
                    ref="youtube"
                    width="560"
                    height="315"
                    :video-id="videos[play].videoId"
                    @ended="endVideo"
                    :player-vars="playerVars"
                    class="player embed-responsive-item"
                    picture-in-picture
                  >
                  </youtube>
                </div>
                <div class="mt-2">
                  <div class="audio-player text-center row">
                    <div class="controls-container col-xs-12 text-center">
                      <ul
                        class="controls text-center list-inline d-flex mx-auto pt-2 ml-5 pl-5"
                      >
                        <li>
                          <span @click="playVideo()">
                            <span v-show="isPlay" class="yellow">
                              <i :class="`fas fa-play fa-2x `"></i>
                            </span>
                            <span v-show="!isPlay">
                              <i :class="`fas fa-pause fa-2x`"></i>
                            </span>
                          </span>
                        </li>

                        <li>
                          <span @click="forwardVideo" style="cursor: pointer">
                            <i
                              class="fa fa-forward fa-2x justify-content-center"
                            ></i>
                          </span>
                        </li>
                        <li>
                          <span
                            @click="triggerLoop"
                            :class="loop ? 'yellow' : ''"
                          >
                            <i class="fas fa-redo-alt fa-2x"></i>
                          </span>
                        </li>
                      </ul>
                    </div>
                  </div>
                  <hr />
                </div>
              </div>
              <div class="col-md-4">
                <h2>{{ $t("coming_up") }}</h2>
                <div
                  class="video-card mb-4 overflow-auto"
                  v-for="(video, index) in videos"
                  :key="index"
                >
                  <div
                    class="else-play cursor"
                    :class="
                      videos[play].videoId == video.videoId
                        ? 'selected-border'
                        : ''
                    "
                    @click.prevent="clickedPlaylist(index)"
                  >
                    <img :src="video.thumbnail" alt />

                    <p class="title">
                      {{ video.title }}
                    </p>
                    <a class="anchor" href="#anchor-link"></a>
                  </div>
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
export default {
  components: {
    newLeftBar,
    search,
  },
  computed: {
    player() {
      return this.$refs.youtube.player;
    },
    videoLastId() {
      return this.videos[this.videos.length - 1];
    },
    currentVideo() {
      return this.videos[this.play];
    },
    currentDescription() {
      return this.currentVideo.description.substring(0, 300);
    },
    currentEmbedUrl() {
      return `https://www.youtube.com/embed/${this.currentVideo.videoId}`;
    },
  },
  async asyncData({ $axios, params }) {
    let vid = await $axios.$get(`/playlists/${params.slug}`);
    return { videos: vid.data };
  },
  head() {
    return {
      title: this.currentVideo.title,
      meta: [
        {
          hid: "description",
          name: "description",
          content: this.currentDescription,
        },
        {
          hid: "keywords",
          name: "keywords",
          content: "chillwhispers, video, calm music, music, music video,",
        },
        {
          hid: "og:title",
          name: "og:title",
          content: "Chillwhispers music videos",
        },
        {
          hid: "og:type",
          name: "og:type",
          content: "music",
        },
        {
          hid: "og:url",
          name: "og:url",
          content: "https://chillwhispers.com",
        },
        {
          hid: "og:image",
          name: "og:image",
          content: this.currentVideo.thumbnail,
        },
        {
          hid: "og:site_name",
          name: "og:site_name",
          content: "chillwhispers",
        },
        {
          hid: "og:description",
          name: "og:description",
          content: this.currentDescription,
        },
        {
          hid: "twitter:card",
          name: "twitter:card",
          content: this.currentVideo.title,
        },
        {
          hid: "twitter:url",
          name: "twitter:url",
          content: `https://chillwhispers.com/watch/${this.currentVideo.videoId}`,
        },
        {
          hid: "twitter:creator",
          name: "twitter:creator",
          content: "@2amtech",
        },
        {
          hid: "twitter:title",
          name: "twitter:title",
          content: this.currentVideo.title,
        },
        {
          hid: "twitter:description",
          name: "twitter:description",
          content: this.currentDescription,
        },

        {
          hid: "twitter:image",
          name: "twitter:image",
          content: this.currentVideo.thumbnail,
        },

        {
          hid: "twitter:app:url:iphone",
          name: "twitter:app:url:iphone",
          content: this.currentEmbedUrl,
        },
        {
          hid: "twitter:app:url:ipad",
          name: "twitter:app:url:ipad",
          content: this.currentEmbedUrl,
        },

        {
          hid: "twitter:app:url:googleplay",
          name: "twitter:app:url:googleplay",
          content: this.currentEmbedUrl,
        },
        {
          hid: "twitter:player",
          name: "twitter:player",
          content: this.currentEmbedUrl,
        },
      ],
    };
  },

  data() {
    return {
      isPlay: true,
      isPause: false,
      loop: false,
      playlist: this.$route.params.slug,
      playerVars: {
        autoplay: 1,
      },
      play: 0,
      videos: [],
    };
  },

  mounted() {
    document.addEventListener("keydown", this.move);
    this.getVideoIdParams();
    this.addVideoIdToUrl();
  },
  beforeDestroy() {
    document.removeEventListener("keydown", this.move);
  },
  methods: {
    routeToLang(loc) {
      if (this.$i18n.locale == "en") {
        return loc;
      } else {
        return "/" + this.$i18n.locale + loc;
      }
    },
    playVideo() {
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
    move(e) {
      if (e.keyCode === 76) {
      }

      if (e.keyCode === 78) {
        this.nextVideo();
      }

      if (e.keyCode === 82) {
        this.triggerLoop();
      }

      e.preventDefault();
    },
    triggerLoop() {
      if (this.loop) {
        this.loop = false;
      } else {
        this.loop = true;
      }
    },
    nextVideo() {
      var indexid = this.videos.findIndex((f) => f.id === this.videoLastId.id);

      if (indexid == this.play) {
        this.play = 0;
      } else {
        this.play++;
      }
    },
    addVideoIdToUrl() {
      if (this.play) {
        this.$router.push(
          `/playlists/${this.$route.params.slug}?videoid=${
            this.videos[this.play].videoId
          }`
        );
      }
    },
    toggleSidebar() {
      const sidebar = document.querySelector(".sidebar");
      sidebar.classList.toggle("shown");
    },
    clickedPlaylist(index) {
      this.play = index;
      this.$router.push(
        `/playlists/${this.$route.params.slug}?videoid=${this.videos[index].videoId}`
      );
    },
    getVideoIdParams() {
      var index = this.videos.findIndex(
        (v) => v.videoId === this.$route.query.videoid
      );

      if (this.$route.query.videoid) {
        this.play = index;
      } else {
        this.play = 0;
      }
    },
    forwardVideo() {
      var indexid = this.videos.length - 1;

      if (indexid == this.play) {
        this.play = 0;
      } else {
        this.play++;
      }
      this.$router.push(
        `/playlists/${this.$route.params.slug}?videoid=${
          this.videos[this.play].videoId
        }`
      );
    },
    endVideo() {
      if (this.loop) {
        this.player.playVideo();
      } else {
        this.forwardVideo();
      }
    },
  },
};
</script>

<style scoped>
.ytp-exp-bottom-control-flexbox .ytp-chrome-controls {
  display: -webkit-box;
  display: none;
  display: -webkit-flex;
  display: -moz-box;
  display: -ms-flexbox;
  display: flex;
  display: none !important;
}
.ytp-chrome-controls .ytp-button.ytp-youtube-button,
.ytp-small-mode .ytp-chrome-controls .ytp-button.ytp-youtube-button,
.ytp-larger-tap-buttons.ytp-small-mode
  .ytp-chrome-controls
  .ytp-button.ytp-youtube-button {
  width: 67px;
  display: none !important;
}
.container-video {
  margin-left: 15%;
  /* background-color: white !important; */
}

.container-playlist {
  margin-top: 100px;
}

.background {
  background-color: aqua;
}

.else-play {
  padding-bottom: 37px;
}
</style>
<style>
.c {
  padding-left: 3%;
  padding-top: 50px;
  background: url("~assets/background.png");
  height: 1560px;
  background-repeat: no-repeat; /* Do not repeat the image */
  background-size: cover; /* Resize the background image to cover the entire container */
}

.leftBar {
  background-color: #090909;
  position: fixed;
  margin-right: 100px;
  /* margin-top: 120px; */
  width: 300px;
  height: 100%;
  color: #8422a6;
  border-top-right-radius: 50px;
  opacity: 0.8;
  height: 100%;
  /* width: 160px; */
  position: fixed;
  z-index: 1;
  top: 0;
  left: 0;
  /* background-color: #111; */
  overflow-x: hidden;
  padding-top: 140px;
  padding-bottom: 20px;
}

a {
  color: #8422a6;
}

.next-list {
  background-color: #482139;
  border-top-left-radius: 50px;
}

.next-list h1 {
  padding-top: 20px;
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
.yellow {
  color: violet !important;
}

.iframe-container {
  position: relative;
  width: 100%;
  padding-bottom: 56.25%;
  height: 0;
}
.iframe-container iframe {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.embed-responsive-item {
  position: relative;
}

.wrapper {
  height: 350px;
  padding-bottom: 0;
}

.embed-responsive .embed-responsive-item,
.embed-responsive iframe,
.embed-responsive embed,
.embed-responsive object,
.embed-responsive video {
  top: 0;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border: 0;
}

.fa-2x {
  border: 5px solid white;
  padding: 10px;
  height: 55px;
  width: 55px !important;
  border-radius: 38px;
}
</style>
