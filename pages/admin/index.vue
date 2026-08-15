<template>
  <div>
    <menuAdmin />
  </div>
</template>

<script>
import menuAdmin from "@/components/MenuAdmin";
export default {
  layout: "MenuAdmin",
  middleware: ["admin"],
  components: {
    menuAdmin,
  },
};
</script>

<style>
.container {
  margin-top: 50px;
}
</style>
