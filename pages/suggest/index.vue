<template>
  <div class="row">
    <div class="col-md-2 leftBar">
      <newLeftBar />
    </div>
    <div class="col-md-9">
      <h1>Video Insert Zone</h1>
      <p>
        Let K.i.s.s(Keep it stupid simple),just copy youtube link in
        form,chouse category and subcategory and click on save.
      </p>
      <p>P.S any summision it's moderate in this moment.</p>
      <VideoInsertForm endpoint="/suggest/add-video" />
    </div>
  </div>
</template>

<script>
import newLeftBar from "@/components/newLeftBar";
import VideoInsertForm from "@/components/admin/VideoInsertForm";
export default {
  components: {
    newLeftBar,
    VideoInsertForm,
  },
  layout: "MenuAdmin",
};
</script>

<style>
.leftBar {
  background-color: #090909;
  margin-top: 0px;
  height: 1600px;
  color: #8422a6;
  border-top-right-radius: 50px;
  width: 300px;
  z-index: 1;
  top: 0;
  overflow-x: hidden;
  padding-top: 50px;
  padding-bottom: 20px;
  padding-left: 50px;
}
</style>
