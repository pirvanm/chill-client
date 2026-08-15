<template>
  <div class="body">
    <div class="main">
      <div class="heading">
        <!--            <button type="submit" v-on:click=toggleSidebar()>-->
        <!--                <i class="fa fa-bars"></i>Menu</button>-->

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
        <h1>{{ $t("write_message") }}</h1>
        <div class="container">
          <!--Section: Contact v.2-->
          <section class="mb-4">
            <!--Section heading-->
            <h2 class="h1-responsive font-weight-bold text-center my-4">
              {{ $t("contact_us") }}
            </h2>
            <!--Section description-->
            <p class="text-center w-responsive mx-auto mb-5">
              {{ $t("message_any_question") }}
            </p>

            <div class="row">
              <!--Grid column-->
              <div class="col-md-9 mb-md-0 mb-5">
                <form
                  id="contact-form"
                  name="contact-form"
                  @submit.prevent="submit"
                >
                  <!--Grid row-->
                  <div class="row">
                    <!--Grid column-->
                    <div class="col-md-12">
                      <div class="md-form mb-0">
                        <input
                          type="text"
                          id="name"
                          name="name"
                          class="form-control"
                          v-model="form.name"
                        />
                        <label for="name" class="mt-3 mb-3">{{
                          $t("your_name")
                        }}</label>
                      </div>
                    </div>
                    <!--Grid column-->

                    <!--Grid column-->
                    <div class="col-md-12">
                      <div class="md-form mb-0">
                        <input
                          type="text"
                          id="email"
                          name="email"
                          class="form-control"
                          v-model="form.email"
                        />
                        <label for="email" class="mt-3 mb-3">
                          {{ $t("your_email") }}
                        </label>
                      </div>
                    </div>
                    <!--Grid column-->
                  </div>
                  <!--Grid row-->

                  <!--Grid row-->
                  <div class="row">
                    <div class="col-md-12">
                      <div class="md-form mb-0">
                        <input
                          type="text"
                          id="subject"
                          name="subject"
                          class="form-control"
                          v-model="form.subject"
                        />
                        <label for="subject" class="mt-3 mb-3">
                          {{ $t("subject") }}</label
                        >
                      </div>
                    </div>
                  </div>
                  <!--Grid row-->

                  <!--Grid row-->
                  <div class="row">
                    <!--Grid column-->
                    <div class="col-md-12">
                      <div class="md-form">
                        <textarea
                          type="text"
                          id="message"
                          name="user_message"
                          rows="2"
                          class="form-control md-textarea"
                          v-model="form.message"
                        ></textarea>
                        <label for="message"> {{ $t("your_message") }}</label>
                      </div>
                    </div>
                  </div>
                  <!--Grid row-->

                  <div class="text-center text-md-left">
                    <button
                      type="submit"
                      class="btn btn-primary"
                      :disabled="isBusy"
                    >
                      {{ $t("send") }}
                    </button>
                  </div>

                  <div class="status"></div>
                </form>
              </div>
              <!--Grid column-->

              <!--Grid column-->
              <div class="col-md-3 text-center">
                <ul class="list-unstyled mb-0">
                  <!--                                    <li><i class="fas fa-map-marker-alt fa-2x"></i>-->
                  <!--                                        <p>San Francisco, CA 94126, USA</p>-->
                  <!--                                    </li>-->

                  <li>
                    <i class="fas fa-envelope mt-4 fa-2x"></i>
                    <p>whisperchill4@gmail.com</p>
                  </li>
                </ul>
              </div>
              <!--Grid column-->
            </div>
          </section>
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
  data() {
    return {
      isBusy: false,
      form: {
        name: "",
        email: "",
        subject: "",
        message: "",
      },
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
  methods: {
    submit() {
      this.isBusy = true;
      this.$axios
        .post("/contact", this.form)
        .then((response) => {
          this.isBusy = false;
        })
        .catch((error) => {
          this.isBusy = false;
        });
    },
    toggleSidebar() {
      const sidebar = document.querySelector(".sidebar");
      sidebar.classList.toggle("shown");
    },
  },
};
</script>

<style scoped>
p {
  color: white;
}
label {
  color: #f58aff;
}
/*# sourceMappingURL=style.css.map */
</style>
