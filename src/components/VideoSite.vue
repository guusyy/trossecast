<template>
  <div>
    <!-- <video class="mainVideo" v-on:click="playAndPause()">
      <source :src="video.videoUrls[0]" type="video/mp4" />
    </video> -->
    <div v-if="video.videoUrls.length == 1">
      <video-player
        class="singleVideo"
        @ready="playerReadied"
        :options="videoOptions[0]"
        ref="mainVideo"
      />
    </div>

    <div v-if="video.videoUrls.length > 1" class="videosContainer">
      <div
        class="videoWrapper mainVideo"
        @click="
          playOrPauseVideos($event);
          swapMainVideo($event);
        "
      >
        <video-player
          class="video"
          :options="videoOptions[0]"
          ref="mainVideo"
          @ready="playerReadied"
        />
      </div>
      <div
        class="thumbnailNavigation"
        :style="{
          height: thumbnailHeight + 'px'
        }"
      >
        <template v-for="thumbnailImage in thumbnailsRender">
          <div
            class="thumbnail"
            v-for="thumbnail in thumbnailImage.sec"
            v-bind:key="thumbnail.index"
            :style="{
              marginLeft:
                (windowWidth - 18) / amountOfThumbnails - thumbnailWidth + 'px'
            }"
          >
            <div
              alt=""
              @click="goToTime(thumbnail.time)"
              :style="{
                backgroundImage: 'url(' + thumbnailImage.image + ')',
                backgroundPositionX: thumbnail.backgroundPositionX + 'px',
                backgroundPositionY: thumbnail.backgroundPositionY + 'px',
                width: thumbnailWidth + 'px',
                height: thumbnailHeight + 'px'
              }"
            ></div>
          </div>
        </template>
        <span v-if="thumbnailsRender.length == 0" class="loadingText">
          Loading thumbnails...
        </span>
      </div>
      <div
        v-for="otherVideo in videoOptions.slice(1)"
        v-bind:key="otherVideo.id"
        class="videoWrapper subVideo"
        @click="
          playOrPauseVideos($event);
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
import $ from "jquery";

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
      windowWidth: window.innerWidth,
      amountOfThumbnails: 300,
      mainVideoIsPlaying: false,
      videoOptions: [],
      thumbnails: [],
      thumbnailsRender: [],
      thumbnailWidth: 158 / 2,
      thumbnailHeight: 90 / 2,
      horizontalItemCount: 5,
      verticalItemCount: 5,
      audio: {}
    };
  },
  beforeMount() {
    this.video.videoUrls.forEach(videoUrl => {
      this.videoOptions.push({
        autoplay: true,
        controls: false,
        fluid: true,
        sources: [
          {
            src: videoUrl,
            type: "video/mp4"
          }
        ]
      });
    });

    this.audio = new Audio(this.video.songUrl);
  },
  mounted() {
    this.$nextTick(() => {
      window.addEventListener("resize", this.onResize);
    });
  },
  beforeDestroy() {
    this.audio.pause();
  },
  computed: {
    player() {
      return this.$refs.videoPlayer.player;
    }
  },
  methods: {
    playOrPauseVideos(event) {
      let targetSource = event.srcElement;

      if (this.isMainVideo(targetSource)) {
        // eslint-disable-next-line prettier/prettier
        let mainVideoPlayer = this.$el.querySelector(".mainVideo").childNodes[0].childNodes[0].player;
        let subVideos = this.$el.querySelectorAll(".subVideo");

        if (!mainVideoPlayer.paused()) {
          mainVideoPlayer.pause();
          subVideos.forEach(video => {
            video.childNodes[0].childNodes[0].player.pause();
          });
          this.audio.pause();
        } else {
          mainVideoPlayer.play();
          subVideos.forEach(video => {
            video.childNodes[0].childNodes[0].player.play();
          });
          this.audio.play();
        }
      }
    },
    goToTime(time) {
      // eslint-disable-next-line prettier/prettier
      let mainVideoPlayer = this.$el.querySelector(".mainVideo").childNodes[0].childNodes[0].player;
      let subVideos = this.$el.querySelectorAll(".subVideo");

      mainVideoPlayer.currentTime(time);
      this.audio.currentTime = time;

      subVideos.forEach(video => {
        video.childNodes[0].childNodes[0].player.currentTime(time);
      });
    },
    swapMainVideo(event) {
      let clickedSubVideo = event.srcElement;
      let retiredMainVideo = this.$el.querySelector(".mainVideo");

      retiredMainVideo.classList.add("subVideo");
      retiredMainVideo.classList.remove("mainVideo");
      clickedSubVideo.classList.remove("subVideo");
      clickedSubVideo.classList.add("mainVideo");
    },
    playerReadied(player) {
      this.audio.play(); //starts playing audio when first video is loaded
      this.createThumbnails(player);
    },
    createThumbnails(player) {
      let vm = this;

      const VIDEOSOURCE = player.player_.children_[0];

      let video = $(VIDEOSOURCE)
        .clone()
        .css("display", "none")
        .appendTo("body")[0];

      video.addEventListener("loadeddata", async function() {
        video.pause();

        let count = 1;

        let id = 1;

        let x = 0,
          y = 0;

        let array = [];

        let amountOfThumbnails = vm.amountOfThumbnails;

        for (let i = 1; i <= amountOfThumbnails; i++) {
          array.push(i);
        }

        let canvas;

        let ii, jj;

        for (ii = 0, jj = array.length; ii < jj; ii += vm.horizontalItemCount) {
          //
          for (let startIndex of array.slice(ii, ii + vm.horizontalItemCount)) {
            //
            let backgroundPositionX = x * vm.thumbnailWidth;
            let backgroundPositionY = y * vm.thumbnailHeight;
            let item = vm.thumbnails.find(x => x.id === id);

            if (!item) {
              canvas = document.createElement("canvas");
              canvas.width = vm.thumbnailWidth * vm.horizontalItemCount;
              canvas.height = vm.thumbnailHeight * vm.verticalItemCount;

              vm.thumbnails.push({
                id: id,
                canvas: canvas,
                sec: [
                  {
                    index: startIndex,
                    time: (video.duration / amountOfThumbnails) * startIndex,
                    backgroundPositionX: -backgroundPositionX,
                    backgroundPositionY: -backgroundPositionY
                  }
                ]
              });
            } else {
              canvas = item.canvas;

              item.sec.push({
                index: startIndex,
                time: (video.duration / amountOfThumbnails) * startIndex,
                backgroundPositionX: -backgroundPositionX,
                backgroundPositionY: -backgroundPositionY
              });
            }

            let context = canvas.getContext("2d");

            video.currentTime =
              (video.duration / amountOfThumbnails) * startIndex;

            await new Promise(function(resolve) {
              let event = function() {
                //
                context.drawImage(
                  video,
                  backgroundPositionX,
                  backgroundPositionY,
                  vm.thumbnailWidth,
                  vm.thumbnailHeight
                );

                x++;

                // removing duplicate events
                video.removeEventListener("canplay", event);

                resolve();
              };

              video.addEventListener("canplay", event);
            });

            // 1 thumbnail is generated completely
            count++;
          }

          // reset x coordinate
          x = 0;

          // increase y coordinate
          y++;

          // checking for overflow
          if (count > vm.horizontalItemCount * vm.verticalItemCount) {
            count = 1;
            x = 0;
            y = 0;
            id++;
          }
        }
        // looping through thumbnail list to update thumbnail
        vm.thumbnails.forEach(function(item) {
          // converting canvas to blob to get short url
          item.canvas.toBlob(
            blob => (item.image = URL.createObjectURL(blob)),
            "image/jpeg"
          );

          // deleting unused property
          delete item.canvas;
        });

        console.log("Done creating thumbnails...");
        vm.reloadThumbnails(); //Trigger render
      });
    },
    reloadThumbnails() {
      //Blob image takes really short time before
      //getting a string url, vue's render mechanism doesn't account
      //for this. This method triggers the rerender  of thumbnails.
      let vm = this;
      setTimeout(function() {
        vm.thumbnailsRender = null;
        vm.thumbnailsRender = vm.thumbnails;
      }, 500);
    },
    isMainVideo(element) {
      return element.classList.contains("mainVideo");
    },
    onResize() {
      this.windowWidth = window.innerWidth;
    }
  }
};
</script>

<style>
/* .vjs-control-bar {
  display: none !important;
} */
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
  cursor: pointer;
}
.subVideo {
  margin: 2.5rem;
  width: 25%;
  order: 3;
  cursor: zoom-in;
}
.thumbnailNavigation {
  width: 100%;
  order: 2;
  display: flex;
  flex-wrap: nowrap;
  background-color: lightgray;
}
.mainVideo .video {
  pointer-events: none;
}
.subVideo .video {
  pointer-events: none;
}
.thumbnail {
  transition: all 0.25s;
  transition-timing-function: cubic-bezier(0.17, 0.67, 0.83, 0.67);
  cursor: crosshair;
  /* border: 1px solid grey; */
}
.thumbnail:hover {
  z-index: 2;
  transform: scale(2, 2);
}
.loadingText {
  margin: auto;
}
</style>
