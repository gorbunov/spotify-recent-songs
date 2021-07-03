<template>
  <div id="app">
    <input type="text" style="width: 60vw;" v-model="token"/>
    <button @click="requestAuth" class="request-button">Request New Token</button>
    <ul class="songs">
      <SingleTrackItem class="song" v-for="track in tracks" :track="track.track" :key="track.id"
                       :token="token"></SingleTrackItem>
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
      appClientId: '12ecbb35a1f24b11800adf15afddabad',
      token: '',
      tracks: [],
      error: false,
      errorText: '',
    }
  },
  mounted () {
    console.log(window.location)
    const token = sessionStorage.getItem('spotify-app-token')
    const hash = window.location.hash.split('&')
    let values = {
      access_token: '',
    }
    for (let item of hash) {
      let parts = item.split('=')
      if (parts[0] === '#access_token') {
        parts[0] = 'access_token'
      }
      values[parts[0]] = parts[1]
    }
    console.log(values)

    if (values.access_token !== '') {
      sessionStorage.setItem('spotify-app-token', values.access_token)
      this.token = values.access_token
    }

    if (token === null && values.access_token === '') {
      console.log('requesting auth')
      this.requestAuth()
    } else {
      console.log('updating list')
      this.updateList()
    }
  },
  methods: {
    requestAuth: function () {
      window.location.href = 'https://accounts.spotify.com/authorize?client_id=' + this.appClientId
          + '&response_type=token'
          + '&redirect_uri=https://agitated-sammet-fcc7a0.netlify.app'
          + '&scope=user-read-recently-played'
    },
    updateList: function () {
      axios.get('https://api.spotify.com/v1/me/player/recently-played', {
        headers: {
          'Authorization': 'Bearer ' + this.token,
        },
      }).then((result) => {
        this.error = false
        this.tracks = result.data.items
      }).catch((reject) => {
        console.log(reject)
        this.tracks = []
        this.error = true
        this.errorText = reject.message
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

.request-button {
  font-size: 3em;
  padding: 12px 8px;
  color: green;
}
</style>
