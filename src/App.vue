<template>
  <div id="app">
    <input type="text" style="width: 60vw;" v-model="token"/>
    <button @click="updateList">Request With Token</button>
    <ul class="songs">
      <SingleTrackItem class="song" v-for="track in tracks" :track="track.track" :key="track.id" :token="token"></SingleTrackItem>
    </ul>
    <div class="error" v-if="error">{{ errorText }}</div>
  </div>
</template>

<script>
import axios from 'axios'
import SingleTrackItem from '@/components/SingleTrackItem'

export default {
  name: 'App',
  components: {SingleTrackItem},
  data: function () {
    return {
      token: 'BQBZXyPeJguw52ha-6a1H2rPqxVBiGt6ZQqemEMO8hmXywJpF0rebE4WIfSfmzt2edwmOTYVSPXktH4oViY6BRL0HfxtFVjL5OcifzrgNcqIrMd7z7gOmOinEgxEgbfmmpbWD2XvuSOpk_9pzH7ZNUDZ18L4USe2YlHfHXjf',
      tracks: [],
      error: false,
      errorText: '',
    }
  },
  mounted () {
    this.updateList()
  },
  methods: {
    updateList: function () {
      axios.get('https://api.spotify.com/v1/me/player/recently-played', {
        headers: {
          'Authorization': 'Bearer ' + this.token,
        },
      }).then((result) => {
        this.error = false;
        this.tracks = result.data.items
      }).catch((reject) => {
        console.log(reject);
        this.tracks = []
        this.error = true;
        this.errorText = reject.message;
      })

    },
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.error {
  background-color: darksalmon;
  color: crimson;
}
</style>
