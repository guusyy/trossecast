<template>
  <table v-if="videoData.length">
    <VideoRow
      v-for="video in videoData"
      :key="video.id"
      :video="video"
      :activeRow="activeRow"
      @set-active-row="setActiveRow"
    />
  </table>
</template>

<script>
import VideoRow from "@/components/VideoRow.vue";

export default {
  name: "VideTable",
  components: {
    VideoRow
  },
  data() {
    return {
      activeRow: 0,
      videoData: [
        {
          id: 1,
          date: "Datum",
          duration: "Tijdsduur",
          city: "Stad",
          coordinates: "Coordinaten",
          firstAndLastName: "Voor- en Achternaam",
          videoUrls: [
            "./camera1.mp4",
            "./camera2.mp4",
            "./camera3.mp4",
            "./camera4.mp4"
          ]
        },
        {
          id: 2,
          date: "10-08-1996",
          duration: "2:21:00",
          city: "Nijmegen",
          coordinates: "51.830564, 5.858252",
          firstAndLastName: "Guus Noij",
          videoUrls: ["http://techslides.com/demos/sample-videos/small.mp4"]
        },
        {
          id: 3,
          date: "10-08-1996",
          duration: "2:21:00",
          city: "Nijmegen",
          coordinates: "51.830564, 5.858252",
          firstAndLastName: "Guus Noij",
          videoUrls: ["http://techslides.com/demos/sample-videos/small.mp4"]
        }
      ]
    };
  },
  methods: {
    setActiveRow(activeRow) {
      this.activeRow == activeRow
        ? (this.activeRow = null)
        : (this.activeRow = activeRow);
    }
  }
};
</script>

<style>
table {
  width: 100%;
  border-spacing: 0px;
}
</style>
