<template>
  <tbody>
    <tr class="videoRow" v-on:click="$emit('set-active-row', video.id)">
      <td class="index-td">{{ video.id }}</td>
      <td>{{ video.date }}</td>
      <td>{{ video.duration }}</td>
      <td>{{ video.city }}</td>
      <td>{{ video.coordinates }}</td>
      <td>{{ video.firstAndLastName }}</td>
    </tr>
    <tr v-if="activeRow == video.id">
      <td colspan="6">
        <VideoSite :video="video" />
      </td>
    </tr>
  </tbody>
</template>

<script>
import VideoSite from "@/components/VideoSite.vue";

export default {
  name: "VideoRow",
  components: {
    VideoSite
  },
  props: {
    video: {
      type: Object,
      required: true
    },
    activeRow: {
      type: Number,
      required: true
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
.videoRow {
  cursor: pointer;
}
</style>
