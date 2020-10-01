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
          playAndPause($event);
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
      mainVideoIsPlaying: false,
      videoOptions: [],
      thumbnails: [],
      thumbnailWidth: 158 / 2,
      thumbnailHeight: 90 / 2,
      horizontalItemCount: 5,
      verticalItemCount: 5
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

      var that = player;
      let vm = this;

      var videoSource = player.player_.children_[0];

      var video = $(videoSource)
        .clone()
        .css("display", "none")
        .appendTo("body")[0];

      // videojs element
      var root = $(videoSource).closest(".video-js");

      // control bar element
      var controlBar = root.find(".vjs-control-bar");

      // thumbnail element
      controlBar.append('<div class="vjs-thumbnail"></div>');

      //
      controlBar.on("mousemove", ".vjs-progress-control", function() {
        // getting time
        var time = $(player)
          .find(".vjs-mouse-display .vjs-time-tooltip")
          .text();

        //
        var temp = null;

        // format: 09
        if (/^\d+$/.test(time)) {
          // re-format to: 0:0:09
          time = "0:0:" + time;
        }
        // format: 1:09
        else if (/^\d+:\d+$/.test(time)) {
          // re-format to: 0:1:09
          time = "0:" + time;
        }

        //
        temp = time.split(":");

        // calculating to get seconds
        time = +temp[0] * 60 * 60 + +temp[1] * 60 + +temp[2];

        //
        for (var item of vm.thumbnails) {
          //
          var data = item.sec.find(x => x.index === time);

          // thumbnail found
          if (data) {
            // getting mouse position based on "vjs-mouse-display" element
            var position = controlBar.find(".vjs-mouse-display").position();

            // updating thumbnail css
            controlBar.find(".vjs-thumbnail").css({
              "background-image": "url(" + item.data + ")",
              "background-position-x": data.backgroundPositionX,
              "background-position-y": data.backgroundPositionY,
              left: position.left + 10,
              display: "block"
            });

            // exit
            return;
          }
        }
      });

      // mouse leaving the control bar
      controlBar.on("mouseout", ".vjs-progress-control", function() {
        // hidding thumbnail
        controlBar.find(".vjs-thumbnail").css("display", "none");
      });

      video.addEventListener("loadeddata", async function() {
        //
        video.pause();

        //
        var count = 1;

        //
        var id = 1;

        //
        var x = 0,
          y = 0;

        //
        var array = [];

        //
        var duration = parseInt(that.duration());
        console.log(vm.horizontalItemCount);
        //
        for (var i = 1; i <= duration; i++) {
          array.push(i);
        }

        //
        var canvas;

        //
        var ii, jj;

        for (ii = 0, jj = array.length; ii < jj; ii += vm.horizontalItemCount) {
          //
          for (var startIndex of array.slice(ii, ii + vm.horizontalItemCount)) {
            //
            var backgroundPositionX = x * vm.thumbnailWidth;

            //
            var backgroundPositionY = y * vm.thumbnailHeight;

            //
            var item = vm.thumbnails.find(x => x.id === id);

            if (!item) {
              //

              //
              canvas = document.createElement("canvas");

              //
              canvas.width = vm.thumbnailWidth * vm.horizontalItemCount;
              canvas.height = vm.thumbnailHeight * vm.verticalItemCount;

              //
              vm.thumbnails.push({
                id: id,
                canvas: canvas,
                sec: [
                  {
                    index: startIndex,
                    backgroundPositionX: -backgroundPositionX,
                    backgroundPositionY: -backgroundPositionY
                  }
                ]
              });
            } else {
              //

              //
              canvas = item.canvas;

              //
              item.sec.push({
                index: startIndex,
                backgroundPositionX: -backgroundPositionX,
                backgroundPositionY: -backgroundPositionY
              });
            }

            //
            var context = canvas.getContext("2d");

            //
            video.currentTime = startIndex;

            //
            await new Promise(function(resolve) {
              var event = function() {
                //
                context.drawImage(
                  video,
                  backgroundPositionX,
                  backgroundPositionY,
                  vm.thumbnailWidth,
                  vm.thumbnailHeight
                );

                //
                x++;

                // removing duplicate events
                video.removeEventListener("canplay", event);

                //
                resolve();
              };

              //
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
            //
            count = 1;

            //
            x = 0;

            //
            y = 0;

            //
            id++;
          }
        }

        // looping through thumbnail list to update thumbnail
        vm.thumbnails.forEach(function(item) {
          // converting canvas to blob to get short url
          item.canvas.toBlob(
            blob => (item.data = URL.createObjectURL(blob)),
            "image/jpeg"
          );

          // deleting unused property
          delete item.canvas;
        });
        console.log(vm.thumbnails);
        console.log("done...");
      });

      // playing video to hit "loadeddata" event
      video.play();
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
