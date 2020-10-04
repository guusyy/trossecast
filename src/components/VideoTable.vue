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
          date: "04-10-2020",
          duration: "2:40:25",
          city: "Amsterdam",
          coordinates: "51.830564, 5.858252",
          firstAndLastName: "Jip Sanders",
          videoUrls: [
            "./camera1.mp4",
            "./camera2.mp4",
            "./camera3.mp4",
            "./camera4.mp4"
          ],
          songUrl: "./song.mp3"
        },
        {
          id: 2,
          date: "09-09-2020",
          duration: "2:21:00",
          city: "Nijmegen",
          coordinates: "51.830564, 5.858252",
          firstAndLastName: "Guus Noij",
          videoUrls: [
            "./camera1.mp4",
            "./camera2.mp4",
            "./camera3.mp4",
            "./camera4.mp4"
          ],
          songUrl: "./song.mp3"
        },
        {
          id: 3,
          date: "16-03-2020",
          duration: "1:56:00",
          city: "Rotterdam",
          coordinates: "51.830564, 5.858252",
          firstAndLastName: "Guus Noij",
          videoUrls: [
            "./camera1.mp4",
            "./camera2.mp4",
            "./camera3.mp4",
            "./camera4.mp4"
          ],
          songUrl: "./song.mp3"
        }
      ]
    };
  },
  methods: {
    setActiveRow(activeRow) {
      this.activeRow == activeRow
        ? (this.activeRow = 100000)
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
