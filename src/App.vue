<template>
    <div class="container mt-3">
    <div class="input-group mb-3 p-5">
      <input 
        v-model="searchQuery"
        @keyup.enter="searchChannels"
        class="form-control" 
        placeholder="チャンネルを検索...">
      <span class="input-group-text" @click="searchChannels"><i class="bi bi-search"></i></span>
    </div>

    <ul class="list-group list-group-flush mt-3">
      <li
        v-for="channel in channels"
        :key="channel.id.channelId"
        class="list-group-item d-flex align-items-center p-3"
        @click="fetchChannelVideos(channel.id.channelId)"
      >
        <img 
          :src="channel.snippet.thumbnails.high.url" 
          :alt="channel.snippet.title" 
          class="rounded-circle me-4" 
          style="width: 64px; height: 64px;"
        >
        <h3 class="m-0">{{ channel.snippet.title }}</h3>
      </li>
    </ul>
  </div>

  <VideoPlayer 
    v-if="playlist.length > 0" 
    :key="playlistKey" 
    :playlist='playlist' 
    @remove-video="removeFromPlaylist"
  />
</template>

<script>
import axios from 'axios'
import VideoPlayer from './components/VideoPlayer.vue'

export default {
  components: {
    VideoPlayer
  },
  data() {
    return {
      searchQuery: '',
      channels: [],
      items: [],
      playlist: [],
      playlistKey: 0,
      apiKey: 'AIzaSyBnBB7bftIfpBkz8oZAjEFQje_Vkg3Bcko',
      searchUrl: 'https://www.googleapis.com/youtube/v3/search',
      detailUrl: 'https://www.googleapis.com/youtube/v3/videos'
    }
  },
  methods: {
    async searchChannels() {
      try {
        const response = await axios.get(this.searchUrl, {
          params: {
            part: 'snippet',
            q: this.searchQuery,
            type: 'channel',
            maxResults: 5,
            key: this.apiKey
          }
        })  
        this.channels = response.data.items || []
        this.searchQuery = ''              
      } catch (error) {
        console.error('Error fetching data:', error)
      }
    },
    async fetchChannelVideos(channelId) {
      try {
        const searchResponse = await axios.get(this.searchUrl, {
          params: {
            part: 'id',
            q: '歌ってみた',
            type: 'video',
            channelId: channelId,
            maxResults: 50,
            key: this.apiKey
          }
        })
        const videoIds = searchResponse.data.items.map(video => video.id.videoId).join(',')
        console.log(videoIds)

        const detailResponse = await axios.get(this.detailUrl, {
          params: {
            part: 'snippet,contentDetails',
            id: videoIds,
            key: this.apiKey
          }
        })
        this.items = detailResponse.data.items || []
        for (const item of this.items) {
          const title = item.snippet.title.toLowerCase()
          const duration = item.contentDetails.duration
          if (title.includes('歌ってみた') || title.includes('cover') || title.includes('歌いました')) {
            if (duration.includes('M') && duration !== 'PT1M') {
              this.playlist.push(item)
            }
          }
        }
        this.playlistKey++
      } catch (error) {
        console.error('Error channel videos:', error)
      }
    },
    removeFromPlaylist(index) {
      this.playlist.splice(index, 1)
    }
  }
}
</script>