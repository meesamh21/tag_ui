<template>
    <div>
        <h1>Tags</h1>

        <h2>List of Tags</h2>
        <ul>
            <li v-for="tag in tags" :key="tag.id" style="list-style: none;">
                Name: {{ tag.name }} - Start time: {{ tag.start_time }} - End time: {{ tag.end_time }}
                <button @click="deleteTag(tag.id)">X</button>
            </li>
        </ul>

        <h2>Search Tag</h2>
        <label for="search-name">Search</label>
        <input id="search-name" v-model="searchName" placeholder="Search Name">
        <button @click="findTag(searchName)">Search</button>
        <div v-if="searchResult.name">
            Name: {{ searchResult.name }} - Start time: {{ searchResult.start_time }} - End time: {{ searchResult.end_time }}
        </div>
        
        <div>
          <h2>Create New Tag</h2>
          <form>
              <label for="name">Name</label>
              <input id="name" v-model="newTag.name" placeholder="Tag Name">

              <label for="start-time">Start Time</label>
              <input id="start-time" v-model="newTag.startTime" placeholder="Start time">

              <label for="end-time">End Time</label>
              <input id="end-time" v-model="newTag.endTime" placeholder="End Time">

              <button type="submit" @click="saveTag">Save</button>
          </form>
        </div>

        <div>
          <br>
          <h2>Video</h2>
            <video-player  class="video-player-box"
                ref="videoPlayer"
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

    </div>
</template>

<script>
import axios from 'axios'
import $ from 'jquery'

export default {
    data: function () {
        return {
            tags: [],
            searchName: "",
            searchResult: {},
            newTag: {
                name: "",
                startTime: "",
                endTime: ""
            },
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
            console.log('player pause!', player)
            this.newTag.startTime = $(".vjs-current-time-display").text()
            this.newTag.endTime = '0:' + (parseInt(this.newTag.startTime.split(':')[1]) + 5).toString()
        },
        playerStateChanged(playerCurrentState) {
            console.log('player current update state', playerCurrentState)
        },

        // player is ready
        playerReadied(player) {
            console.log('the player is readied', player)
        },
        saveTag(e) {
            e.preventDefault()
            axios.post("http://localhost:3000/tag", {
                name: this.newTag.name,
                start_time: this.newTag.startTime,
                end_time: this.newTag.endTime
            })
            .then(() => {
                this.getTags()
                this.newTag = {
                    name: "",
                    startTime: "",
                    endTime: ""
                }
            })
        },
        getTags() {
            axios.get("http://localhost:3000/tags")
            .then(response => {
                this.tags = response.data
            })
        },
        findTag(name) {
            axios.get(`http://localhost:3000/tag?name=${name}`)
            .then(response => {
                this.searchResult = response.data
                const seconds = parseInt(this.searchResult.start_time.split(':')[1])
                this.player.currentTime(seconds)
            })
        },
        deleteTag(id) {
            axios.delete(`http://localhost:3000/tag/${id}`)
            .then(response => {
                console.log(response)
                this.getTags()
            })
        }
    },
    created() {
        this.getTags()
    }
}
</script>

<style scoped>
.video-player-box {
    position: relative !important;
}
</style>