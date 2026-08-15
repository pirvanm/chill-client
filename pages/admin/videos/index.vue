<template>
  <div>
    <div class="row">
      <div class="col-2">
        <menuAdmin />
      </div>

      <div class="col-10">
        <h1>Video Insert Zone</h1>
        <VideoInsertForm endpoint="/admin/add-video" show-preview />
      </div>
    </div>
  </div>
</template>

<script>
import menuAdmin from "@/components/MenuAdmin";
import VideoInsertForm from "@/components/admin/VideoInsertForm";
export default {
  components: {
    menuAdmin,
    VideoInsertForm,
  },
  layout: "MenuAdmin",
  middleware: ["admin"],
};
</script>
