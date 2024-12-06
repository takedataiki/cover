<template>
  <div class="bg-white w-100 h-100 position-absolute top-0 start-0">
    <div ref="player"></div>
    <div class="d-flex flex-column align-items-center justify-content-center">
        <div class="mt-3 d-flex align-items-center">
          <button 
            class="rounded-circle shadow-lg border-white me-3" 
            style="width: 50px; height: 50px; border: 3px solid white; background-color: transparent;" 
            @click="previousVideo" 
            :disabled="currentVideoIndex === 0"
          >
            <i class="bi bi-skip-backward"></i>
          </button>
          <button @click="togglePlay" 
            class="rounded-circle shadow-lg" 
            style="width: 75px; height: 75px; border: 3px solid white; background-color: transparent;"
          >
            <i class="bi bi-play-fill display-6 ps-2" v-if="!isPlaying"></i>
            <i class="bi bi-pause-fill display-6" v-else></i>
          </button>
          <button 
            class="rounded-circle shadow-lg border-white ms-3" 
            style="width: 50px; height: 50px; border: 3px solid white; background-color: transparent;"
            @click="nextVideo" 
            :disabled="currentVideoIndex === playlist.length - 1"
          >
            <i class="bi bi-skip-forward"></i>
          </button>
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
    }
  }
}
</script>