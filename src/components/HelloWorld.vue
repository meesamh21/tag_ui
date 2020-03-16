<template>
  <div>
    <br>
    <h2>Video</h2>
      <video-player
          :options="playerOptions"
          :playsinline="true"
          customEventName="customstatechangedeventname"
          @play="onPlayerPlay($event)"
          @pause="onPlayerPause($event)"
          @ended="onPlayerEnded($event)"
          @waiting="onPlayerWaiting($event)"
          @statechanged="playerStateChanged($event)"
          @ready="playerReadied">
      </video-player>
  </div>
</template>

<script>
import $ from 'jquery'

export default {
    data: function () {
        return {
            playerOptions: {
            // videojs options
                muted: true,
                language: 'en',
                playbackRates: [0.7, 1.0, 1.5, 2.0],
                sources: [{
                    type: "video/mp4",
                    src: "https://cdn.theguardian.tv/webM/2015/07/20/150716YesMen_synd_768k_vp8.webm"
                }],
                poster: "/static/images/author.jpg",
            }
        }
    },
    mounted() {
        console.log('this is current player instance object', this.player)
    },
    computed: {
        player() {
            return this.$refs.videoPlayer.player
        }
    },
    methods: {
        onPlayerPlay(player) {
            console.log('player play!', player)
        },
        onPlayerPause(player) {
            console.log('player pause!', player, this.player)
            this.$emit('start-time' ,$(".vjs-current-time-display").text())
        },
        playerStateChanged(playerCurrentState) {
            console.log('player current update state', playerCurrentState)
        },

        // player is ready
        playerReadied(player) {
            console.log('the player is readied', player)
        }
    }
}
</script>