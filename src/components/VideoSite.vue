<template>
  <div>
    <!-- <video class="mainVideo" v-on:click="playAndPause()">
      <source :src="video.videoUrls[0]" type="video/mp4" />
    </video> -->
    <video-player
      class="mainVideo"
      :options="videoOptions[0]"
      @play="onPlayerPlay($event)"
      @pause="onPlayerPause($event)"
      @statechanged="playerStateChanged($event)"
    />

    <div v-if="video.videoUrls.length > 1" class="subVideosContainer">
      <div
        v-for="otherVideo in videoOptions.slice(1)"
        v-bind:key="otherVideo.id"
        class="subVideoWrapper"
      >
        <!-- <video class="subVideo">
          <source :src="otherVideos" type="video/mp4" />
        </video> -->
        <video-player
          class="subVideo"
          :options="otherVideo"
          @play="onPlayerPlay($event)"
          @pause="onPlayerPause($event)"
          @statechanged="playerStateChanged($event)"
        />
      </div>
    </div>
  </div>
</template>

<script>
import VideoPlayer from "@/components/VideoPlayer.vue";

import "video.js/dist/video-js.css";

export default {
  name: "VideoSite",
  components: {
    VideoPlayer
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
      videoOptions: []
    };
  },
  beforeMount() {
    this.video.videoUrls.forEach(videoUrl => {
      this.videoOptions.push({
        autoplay: true,
        controls: true,
        fluid: true,
        sources: [
          {
            src: videoUrl,
            type: "video/mp4"
          }
        ]
      });
    });
  },
  methods: {
    playAndPause() {
      let subVideos = this.$el.querySelectorAll(".subVideo");
      let mainVideo = this.$el.querySelector(".mainVideo");

      subVideos.forEach(element => {
        mainVideo.paused ? element.play() : element.pause();
      });

      mainVideo.paused ? mainVideo.play() : mainVideo.pause();
    },
    onPlayerPause(player) {
      console.log("player pause!", player);
    },
    // or listen state event
    playerStateChanged(playerCurrentState) {
      console.log("player current update state", playerCurrentState);
    }
  }
};
</script>

<style>
.vjs-control-bar {
  display: none !important;
}
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
  width: 25%;
  margin: 2.5rem;
}
.subVideosContainer {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}
</style>
