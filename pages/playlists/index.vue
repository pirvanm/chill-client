<template>
  <div class="body">
    <div class="main">
      <div class="heading">
        <button type="submit" @click="toggleSidebar">
          <i class="fas fa-bars"></i>
        </button>
        <span class="title">Chillwhispers</span>
      </div>
      <newLeftBar />
      <div class="content">
        <div>
          <search />
        </div>

        <div class="row">
          <div class="col-lg-2 d-none d-sm-block">
            <div class="container ml-3">
              <h4 class="mt-3">{{ $t("filter_by") }}</h4>

              <h4 @click="toggle">{{ $t("popularity") }}</h4>
              <FilterRadioGroup
                v-show="shown"
                :options="popularityOptions"
                @select="key = $event"
              />

              <h4 class="mt-3" @click="toggle2">{{ $t("duration") }}</h4>
              <FilterRadioGroup
                v-show="shown2"
                :options="durationOptions"
                @select="key = $event"
              />

              <h4 class="mt-3" @click="toggle3">{{ $t("categories") }}</h4>
              <FilterRadioGroup
                v-show="shown3"
                :options="categoryOptions"
                @select="key = $event"
              />

              <h4 class="mt-3" @click="toggle4">
                {{ $t("countries_regional") }}
              </h4>
              <FilterRadioGroup
                v-show="shown4"
                :options="regionalOptions"
                @select="key = $event"
              />
            </div>
          </div>
          <div class="col-lg-10">
            <CategoryGrid
              v-if="currentGrid"
              :heading="currentGridHeading"
              :items="currentGrid.items"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import newFooter from "@/components/newFooter";
import newLeftBar from "@/components/newLeftBar";
import search from "@/components/Search";
import CategoryGrid from "@/components/playlists/CategoryGrid";
import FilterRadioGroup from "@/components/playlists/FilterRadioGroup";

export default {
  components: {
    newLeftBar,
    newFooter,
    search,
    CategoryGrid,
    FilterRadioGroup,
  },
  async asyncData({ $axios, params }) {
    let playlist = await $axios.$get(`/playlists`);
    let chann = await $axios.$get(`/channels`);

    return {
      shown: false,
      shown2: false,
      shown3: true,
      shown4: false,
      key: 26,
      playlists: playlist.playlists,
      tops: playlist.top,
      rock: playlist.rock,
      chill: playlist.chill,
      long: playlist.long,
      quick: playlist.quick,
      meditat: playlist.meditat,
      downtempo: playlist.downtempo,
      all: playlist.all,
      ambiental: playlist.ambiental,
      gaming: playlist.gaming,
      classic: playlist.classic,
      lo_fi: playlist.lo_fi,
      hiphop: playlist.hiphop,
      african: playlist.african,
      spania: playlist.spania,
      china: playlist.china,
      arabic: playlist.arabic,
      chinese: playlist.chinese,
      france: playlist.france,
      indian: playlist.indian,
      italy: playlist.italy,
      spanish: playlist.spanish,
      japan: playlist.japan,
      techno: playlist.techno,
      trap: playlist.trap,
      channels: chann.data,
    };
  },
  computed: {
    // The "Rock" filter (key 4) is intentionally omitted here: the original
    // markup guarded it behind `v-if="statusTogggle"`, a variable that was
    // never defined anywhere, so it always evaluated falsy and the radio
    // never rendered. Preserving that (buggy) behavior rather than silently
    // re-enabling a filter that's been invisible to users.
    popularityOptions() {
      return [{ key: 1, label: "Top", checked: true }];
    },
    durationOptions() {
      return [
        { key: 2, label: "Quick", checked: false },
        { key: 3, label: "Long", checked: true },
      ];
    },
    categoryOptions() {
      return [
        { key: 5, label: "Chill", checked: true },
        { key: 6, label: "Meditate", checked: false },
        { key: 7, label: "Popular", checked: true },
        { key: 9, label: "Ambiental", checked: false },
        { key: 10, label: "Gaming", checked: false },
        { key: 11, label: "Classic", checked: true },
        { key: 12, label: "Lo-Fi", checked: false },
        { key: 13, label: "HipHop", checked: true },
        { key: 24, label: "Techno", checked: false },
        { key: 25, label: "Trap", checked: false },
      ];
    },
    regionalOptions() {
      return [
        { key: 14, label: "African", checked: false },
        { key: 15, label: "Spania", checked: true },
        { key: 22, label: "Spanish", checked: false },
        { key: 16, label: "China", checked: false },
        { key: 18, label: "Chinese", checked: false },
        { key: 17, label: "Arabic", checked: true },
        { key: 19, label: "France", checked: false },
        { key: 20, label: "Indian", checked: false },
        { key: 21, label: "Italy", checked: false },
        { key: 23, label: "Japan", checked: false },
      ];
    },
    categoryGrids() {
      return [
        { key: 1, heading: "Top Category", items: this.tops },
        { key: 2, heading: "Quick Category", items: this.quick },
        { key: 3, heading: "Long Category", items: this.long },
        { key: 4, heading: "Rock Playlist", items: this.rock },
        { key: 5, heading: "Chill Category", items: this.chill },
        { key: 6, heading: "Meditate Category", items: this.meditat },
        { key: 7, heading: "Down Tempo Category", items: this.downtempo },
        { key: 8, heading: "All Category", items: this.all },
        { key: 9, heading: "Ambiental Category", items: this.ambiental },
        { key: 10, heading: "Gaming Category", items: this.gaming },
        { key: 11, heading: "Classic Category", items: this.classic },
        { key: 12, heading: "Lo-Fi Category", items: this.lo_fi },
        { key: 13, heading: "HipHop Category", items: this.hiphop },
        { key: 14, heading: "African Category", items: this.african },
        { key: 15, heading: "Spania Category", items: this.spania },
        { key: 16, heading: "China Category", items: this.china },
        { key: 17, heading: "Arabic Category", items: this.arabic },
        { key: 18, heading: "Chinese Category", items: this.chinese },
        { key: 19, heading: "France Category", items: this.france },
        { key: 20, heading: "Indian Category", items: this.indian },
        { key: 21, heading: "Italy Category", items: this.italy },
        { key: 22, heading: "Spanish Category", items: this.spanish },
        { key: 23, heading: "Japan Category", items: this.japan },
        { key: 24, heading: "Techno Category", items: this.techno },
        { key: 25, heading: "Trap Category", items: this.trap },
        { key: 26, heading: null, items: this.playlists },
      ];
    },
    currentGrid() {
      return this.categoryGrids.find((grid) => grid.key === this.key);
    },
    currentGridHeading() {
      // key 26 (the default, "top playlists") is the only translated heading
      // in the original markup -- the rest were hard-coded English strings.
      return this.currentGrid.key === 26
        ? this.$t("top_playlists")
        : this.currentGrid.heading;
    },
  },
  methods: {
    routeToLang(loc) {
      if (this.$i18n.locale == "en") {
        return loc;
      } else {
        return "/" + this.$i18n.locale + loc;
      }
    },
    toggle() {
      this.shown = !this.shown;
    },
    toggle2() {
      this.shown2 = !this.shown2;
    },
    toggle3() {
      this.shown3 = !this.shown3;
    },
    toggle4() {
      this.shown4 = !this.shown4;
    },
    toggleSidebar() {
      const sidebar = document.querySelector(".sidebar");
      sidebar.classList.toggle("shown");
    },
  },
};
</script>

<style scoped>
.c {
  padding-left: 3%;
  padding-top: 50px;
  background: url("~assets/background.png");
  height: 5000px;
  background-repeat: no-repeat; /* Do not repeat the image */
  background-size: cover; /* Resize the background image to cover the entire container */
}

.leftBar {
  background-color: #090909;
  position: fixed;
  margin-right: 100px;
  /* margin-top: 120px; */
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
  width: 300px;
}
h3,
a {
  color: #f58aff;
}

h3 {
  border-bottom: 2px solid #2ecc71;
  padding-bottom: 1rem;
}

h4 {
  border-bottom: 2px solid #fff;
  color: white;
  padding-bottom: 1rem;
}

label.btn span {
  font-size: 1.5em;
}

label input[type="radio"] ~ i.fa.fa-circle-o {
  color: #c8c8c8;
  display: inline;
}
label input[type="radio"] ~ i.fa.fa-dot-circle-o {
  display: none;
}
label input[type="radio"]:checked ~ i.fa.fa-circle-o {
  display: none;
}
label input[type="radio"]:checked ~ i.fa.fa-dot-circle-o {
  color: #7aa3cc;
  display: inline;
}
label:hover input[type="radio"] ~ i.fa {
  color: #7aa3cc;
}

label input[type="checkbox"] ~ i.fa.fa-square-o {
  color: #c8c8c8;
  display: inline;
}
label input[type="checkbox"] ~ i.fa.fa-check-square-o {
  display: none;
}
label input[type="checkbox"]:checked ~ i.fa.fa-square-o {
  display: none;
}
label input[type="checkbox"]:checked ~ i.fa.fa-check-square-o {
  color: #7aa3cc;
  display: inline;
}
label:hover input[type="checkbox"] ~ i.fa {
  color: #7aa3cc;
}

div[data-toggle="buttons"] label.active {
  color: #7aa3cc;
}

div[data-toggle="buttons"] label {
  display: inline-block;
  padding: 6px 12px;
  margin-bottom: 0;
  font-size: 14px;
  font-weight: normal;
  line-height: 2em;
  text-align: left;
  white-space: nowrap;
  vertical-align: top;
  cursor: pointer;
  background-color: none;
  border: 0px solid #c8c8c8;
  border-radius: 3px;
  color: #c8c8c8;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  -o-user-select: none;
  user-select: none;
}

div[data-toggle="buttons"] label:hover {
  color: #7aa3cc;
}

div[data-toggle="buttons"] label:active,
div[data-toggle="buttons"] label.active {
  -webkit-box-shadow: none;
  box-shadow: none;
}

/*  submenu logic */
.dropdown-submenu {
  position: relative;
}
.dropdown-submenu > .dropdown-menu {
  top: 0;
  left: 100%;
  margin-top: -6px;
  margin-left: -1px;
}
.dropdown-submenu:hover > .dropdown-menu {
  display: block;
}
.dropdown-submenu:hover > a:after {
  border-left-color: #fff;
}
.dropdown-submenu.pull-left {
  float: none;
}
.dropdown-submenu.pull-left > .dropdown-menu {
  left: -100%;
  margin-left: 10px;
}
</style>
