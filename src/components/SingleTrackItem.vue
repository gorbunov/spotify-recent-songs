<template>
  <li class="track">
    <a :href="link">{{ name }} - {{ artistsText }}</a>
  </li>
</template>

<script>
export default {
  name: 'SingleTrackItem',
  data () {
    return {
      link: this.$props.track.preview_url,
      name: this.$props.track.name,
      artists: this.$props.track.artists,
      userToken: this.$props.token,
    }
  },
  computed: {
    artistsText: function () {
      const names = this.artists.map(artist => artist.name)
      return names.join(', ');
    }
  },
  props: {
    track: {
      type: Object,
      default: function () {
        return {
          href: '',
          preview_url: '',
          name: '',
          artists: [],
        }
      },
    },
    token: {
      type: String,
      default: '',
    }
  },
}
</script>

<style scoped>
  li.song {
    text-align: left;
    font-size: 1.2em;
    padding-bottom: .2em;
  }
</style>