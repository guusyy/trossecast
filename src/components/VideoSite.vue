<template>
  <div>
    <!-- <video class="mainVideo" v-on:click="playAndPause()">
      <source :src="video.videoUrls[0]" type="video/mp4" />
    </video> -->
    <div v-if="video.videoUrls.length == 1" @click="playAndPause()">
      <video-player
        class="mainVideo"
        :options="videoOptions[0]"
        ref="mainVideo"
      />
    </div>

    <div v-if="video.videoUrls.length > 1" class="videosContainer">
      <div
        class="videoWrapper mainVideo"
        @click="
          playAndPause($event);
          swapMainVideo($event);
        "
      >
        <video-player
          class="video"
          :options="videoOptions[0]"
          ref="mainVideo"
        />
      </div>
      <div
        v-for="otherVideo in videoOptions.slice(1)"
        v-bind:key="otherVideo.id"
        class="videoWrapper subVideo"
        @click="
          playAndPause($event);
          swapMainVideo($event);
        "
      >
        <!-- <video class="subVideo">
          <source :src="otherVideos" type="video/mp4" />
        </video> -->
        <video-player class="video" :options="otherVideo" ref="subVideos" />
      </div>
    </div>
  </div>
</template>

<script>
import { videoPlayer } from "vue-video-player";

import "video.js/dist/video-js.css";

export default {
  name: "VideoSite",
  components: {
    videoPlayer
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
  mounted() {
    let mainVideo = this.$el.querySelectorAll(".vjs-tech");

    mainVideo[0].pause();
  },
  computed: {
    player() {
      return this.$refs.videoPlayer.player;
    }
  },
  methods: {
    playAndPause(event) {
      let targetSource = event.srcElement;

      if (targetSource.classList.contains("mainVideo")) {
        // eslint-disable-next-line prettier/prettier
        let mainVideoPlayer = this.$el.querySelector(".mainVideo").childNodes[0].childNodes[0].player;
        let subVideos = this.$el.querySelectorAll(".subVideo");

        !mainVideoPlayer.paused()
          ? mainVideoPlayer.pause()
          : mainVideoPlayer.play();

        subVideos.forEach(element => {
          if (!mainVideoPlayer.paused()) {
            element.childNodes[0].childNodes[0].player.play();
          } else {
            element.childNodes[0].childNodes[0].player.pause();
          }
        });
      }
    },
    swapMainVideo(event) {
      let target = event.srcElement;
      let mainVideoPlayer = this.$el.querySelector(".mainVideo");

      mainVideoPlayer.classList.add("subVideo");
      mainVideoPlayer.classList.remove("mainVideo");
      target.classList.remove("subVideo");
      target.classList.add("mainVideo");
    },
    playerReadied(player) {
      console.log("the player is readied", player);
      // you can use it to do something...
      // player.[methods]
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
.videosContainer {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}
.mainVideo {
  order: 1;
  width: 100%;
}
.subVideo {
  margin: 2.5rem;
  width: 25%;
  order: 2;
}
.mainVideo .video {
  pointer-events: none;
}
.subVideo .video {
  pointer-events: none;
}
</style>
