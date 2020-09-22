<template>
  <div>
    <video class="mainVideo" v-on:click="playAndPause()">
      <source :src="video.videoUrls[0]" type="video/mp4" />
    </video>
    <!-- <video-player :options="videoOptions" /> -->

    <div v-if="video.videoUrls.length > 1" class="subVideosContainer">
      <div
        v-for="otherVideos in video.videoUrls.slice(1)"
        v-bind:key="otherVideos.id"
        class="subVideoWrapper"
      >
        <video class="subVideo">
          <source :src="otherVideos" type="video/mp4" />
        </video>
      </div>
    </div>
  </div>
</template>

<script>
// import VideoPlayer from "@/components/VideoPlayer.vue";

export default {
  name: "VideoSite",
  components: {
    // VideoPlayer
  },
  props: {
    video: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      mainVideoIsPlaying: false,
      videoOptions: {
        autoplay: true,
        controls: true,
        sources: [
          {
            src: "camera1.mp4",
            type: "video/mp4"
          }
        ]
      }
    };
  },
  methods: {
    playAndPause() {
      let subVideos = this.$el.querySelectorAll(".subVideo");
      let mainVideo = this.$el.querySelector(".mainVideo");

      subVideos.forEach(element => {
        mainVideo.paused ? element.play() : element.pause();
      });

      mainVideo.paused ? mainVideo.play() : mainVideo.pause();
    }
  }
};
</script>

<style>
td {
  border-bottom: solid 1px lightgrey;
  padding: 0.5rem 0px;
  font-weight: 700;
}

.index-td {
  font-weight: 400;
}
.mainVideo {
  width: 100%;
}
.subVideo {
  width: 100%;
}
.subVideoWrapper {
  min-width: 150px;
  max-width: 20%;
  margin: 2.5rem;
}
.subVideosContainer {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}
</style>
