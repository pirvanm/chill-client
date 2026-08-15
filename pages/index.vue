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
          <div class="d-flex mb-5" v-if="isAdmin">
            <button class="btn-info btn">
              <nuxt-link to="/admin" class="text-dark">Admin </nuxt-link>
            </button>
          </div>
          <search />
        </div>

        <div class="clearfix"></div>
        <h1 class="ml-5">{{ $t("popular_song") }}</h1>
        <div class="container">
          <div class="row">
            <div
              class="col-lg-4 col-md-6 mb-4"
              v-for="(vid, index) in videos"
              :key="index"
            >
              <div class="video-card">
                <nuxt-link
                  :to="routeToLang(`/watch?v=${vid.videoId}`)"
                  class="text-dark"
                >
                  <img :src="vid.thumbnail" />
                  <p class="title">
                    {{ vid.title }}
                  </p>
                </nuxt-link>
                <a href="#" class="category">{{
                  vid.category.category_name
                }}</a>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <b-modal
      id="suggestion"
      size="xl"
      no-close-on-backdrop
      centered
      hide-footer
      title="Choose what u like!"
    >
      <suggestion-modal @saved="closeSuggestionModal" />
    </b-modal>
  </div>
</template>

<script>
import newLeftBar from "@/components/newLeftBar";
import search from "@/components/Search";
import SuggestionModal from "../components/suggestionModal.vue";

export default {
  name: "Home",
  components: {
    newLeftBar,
    search,
    SuggestionModal,
  },

  async asyncData({ $axios, params }) {
    let data = await $axios.$get(`/list-home-videos`);

    return { videos: data.data };
  },
  data() {
    return {
      isAdmin: false,
    };
  },

  head: {
    title: "Chillwhispers ",
    meta: [
      {
        hid: "description",
        name: "description",
        content: "Chillwhispers Home Page",
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
        content: "https://i.ytimg.com/vi/n0svuurLibQ/mqdefault.jpg",
      },
      {
        hid: "og:site_name",
        name: "og:site_name",
        content: "chillwhispers",
      },
      {
        hid: "og:description",
        name: "og:description",
        content: "Chillwhispers Home Page",
      },
    ],
  },
  created() {
    if (this.$auth.loggedIn) {
      this.isAdmin = this.$auth.user.isAdmin;
      if (this.$auth.user.step == 1) {
        this.$bvModal.show("suggestion");
      }
    }
  },

  methods: {
    routeToLang(loc) {
      if (this.$i18n.locale == "en") {
        return loc;
      } else {
        return "/" + this.$i18n.locale + loc;
      }
    },
    closeSuggestionModal() {
      this.$bvModal.hide("suggestion");
    },
    toggleSidebar() {
      const sidebar = document.querySelector(".sidebar");
      sidebar.classList.toggle("shown");
    },
  },
};
</script>

<style scoped>
.fa-2x {
  border: 5px solid white;
  padding: 11px;
  height: 55px;
  width: 55px;
  border-radius: 38px;
}

/*# sourceMappingURL=style.css.map */
</style>
