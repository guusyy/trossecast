<template>
  <div class="home">
    <!-- <HelloWorld msg="Welcome to Your Vue.js App" /> -->
    <VideoTable />
  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from "@/components/HelloWorld.vue";
import VideoTable from "@/components/VideoTable.vue";

export default {
  name: "Home",
  components: {
    VideoTable
    // HelloWorld
  },
};
</script>

<style>
</style>