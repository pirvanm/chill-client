<template>
  <div class="container">
    <div class="row">
      <div class="col-3">
        <menuAdmin />
      </div>
      <div class="col-9">
        <div class="row">
          <div class="card" v-for="stat in statCards" :key="stat.key">
            <img src="" class="card-img-top" alt="" />
            <div class="card-body">
              <h5 class="card-title">{{ stat.label }}</h5>
              <p class="card-text">{{ stats[stat.key] }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import menuAdmin from "@/components/MenuAdmin";

export default {
  middleware: ["admin"],
  components: {
    menuAdmin,
  },
  async asyncData({ $axios, params }) {
    let statistics = await $axios.$get(`/stats`);

    return { stats: statistics };
  },
  data() {
    return {
      statCards: [
        { label: "Total All", key: "count0" },
        { label: "ChillHop", key: "count1" },
        { label: "Ambiental", key: "count2" },
        { label: "Lofi", key: "count3" },
        { label: "Regional", key: "count4" },
        { label: "Chillstep", key: "count5" },
        { label: "ChillOut", key: "count6" },
        { label: "Downtempo", key: "count7" },
        { label: "Trap", key: "count8" },
        { label: "Classic", key: "count9" },
        { label: "Classical", key: "count10" },
        { label: "Lounge", key: "count11" },
        { label: "World", key: "count12" },
        { label: "Techno", key: "count13" },
        { label: "Rock", key: "count14" },
      ],
    };
  },
};
</script>
