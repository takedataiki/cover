<template>
  <div class="bg-white w-100 h-100 position-absolute top-0 start-0">
    <div class="d-flex flex-column align-items-center justify-content-center container">
      <div ref="player"></div>
      <div class="mt-3 d-flex align-items-center">
        <button 
          class="rounded-circle shadow border-white me-3 text-black" 
          style="width: 48px; height: 48px; border: 3px solid white; background-color: transparent;" 
          @click="previousVideo" 
          :disabled="currentVideoIndex === 0"
        >
          <i class="bi bi-skip-backward-fill"></i>
        </button>
        <!-- <div 
          class="rounded-circle shadow border border-white border-5 text-black bg-light d-flex align-items-center justify-content-center" 
          style="width: 48px; height: 48px;" 
          @click="previousVideo" 
          :disabled="currentVideoIndex === 0"
        >
          <i class="bi bi-skip-backward-fill"></i>
        </div> -->
        <button @click="togglePlay" 
          class="rounded-circle shadow text-black" 
          style="width: 64px; height: 64px; border: 3px solid white; background-color: transparent;"
        >
          <i class="bi bi-play-fill display-6 ps-1" v-if="!isPlaying"></i>
          <i class="bi bi-pause-fill display-6" v-else></i>
        </button>
        <!-- <button 
          @click="togglePlay" 
          class="rounded-circle shadow border border-white border-5 text-black bg-light d-flex align-items-center justify-content-center ms-3 me-3" 
          style="width: 64px; height: 64px;"
        >
          <i class="bi bi-play-fill display-6 ps-1" v-if="!isPlaying"></i>
          <i class="bi bi-pause-fill display-6" v-else></i>
        </button> -->
        <button 
          class="rounded-circle shadow border-white ms-3 text-black" 
          style="width: 48px; height: 48px; border: 3px solid white; background-color: transparent;"
          @click="nextVideo" 
          :disabled="currentVideoIndex === playlist.length - 1"
        >
          <i class="bi bi-skip-forward-fill"></i>
        </button>
        <!-- <div 
          class="rounded-circle shadow border border-white border-5 text-black bg-light d-flex align-items-center justify-content-center" 
          style="width: 48px; height: 48px;" 
          @click="previousVideo" 
          :disabled="currentVideoIndex === 0"
        >
          <i class="bi bi-skip-forward-fill"></i>
        </div> -->
      </div>
      <ul class="list-group list-group-flush w-100 mt-5">
        <li
          v-for="(video, index) in playlist"
          :key="index"
          class="list-group-item p-3 d-flex align-items-center"
          :class="{ 'bg-secondary-subtle': currentVideoIndex === index}"
          @click="playVideo(index)"
        >
          <img
            :src="video.snippet.thumbnails.high.url"
            class="w-25 rounded"
          >
          <div class="ms-3">
            <p>{{ video.snippet.title }}</p>
          </div>
          <i class="bi bi-trash ms-auto ps-1" @click.stop="removeList(index)"></i>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'VideoPlayer',
  props: {
    playlist: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      player: '',
      isPlaying: false,
      currentVideoIndex: 0
    }
  },
  mounted() {
    const tag = document.createElement('script')
    tag.src = 'https://www.youtube.com/iframe_api'
    const firstScriptTag = document.getElementsByTagName('script')[0]
    firstScriptTag.parentNode.insertBefore(tag, firstScriptTag)

    window.onYouTubeIframeAPIReady = () => {
      this.initPlayer()
    }
  },
  methods: {
    initPlayer() {
      const width = window.innerWidth
      const height = Math.round(width * 0.5625)

      this.player = new window.YT.Player(this.$refs.player, {
        height: height.toString(),
        width: width.toString(),
        videoId: this.playlist[0].id,
        events: {
          onStateChange: this.onPlayerStateChange
        }
      })
    },
    onPlayerStateChange(event) {
      if (event.data === window.YT.PlayerState.ENDED) {
        this.nextVideo()
      }
      this.isPlaying = event.data === window.YT.PlayerState.PLAYING
    },
    playVideo(index) {
      if (index >= 0 && index < this.playlist.length) {
        this.currentVideoIndex = index
        this.player.loadVideoById(this.playlist[index].id)
      }
    },
    nextVideo() {
      if (this.currentVideoIndex < this.playlist.length - 1) {
        this.playVideo(this.currentVideoIndex + 1)
      }
    },
    previousVideo() {
      if (this.currentVideoIndex > 0) {
        this.playVideo(this.currentVideoIndex - 1)
      }
    },
    togglePlay() {
      if (this.isPlaying) {
        this.player.pauseVideo()
      } else {
        this.player.playVideo()
      }
    },
    removeList(index) {
      this.$emit('remove-video', index)
    }
  }
}
</script>