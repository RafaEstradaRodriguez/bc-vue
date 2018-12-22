<template>
  <div id="app">
    <videoframe v-bind:currentVideo="currentVideo" @subsAction="subsAction($event)" @PlaylistAction="playListAction($event)"></videoframe>
    <playlist v-bind:currentVideo="currentVideo" @updatedData="swapData($event)" v-bind:relCoords="plCoords" v-bind:visible="plVisibility"></playlist>
    <audiosubs :class="{active:subsVisible}" v-bind:relCoords="subsCoords"></audiosubs>
  </div>
</template>

<script>

  import videoframe from './components/videoPlayer';
  import playlist from './components/playList';
  import audioSubs from './components/audioSubs';

  export default {

    name: 'app',

    components: {
      'videoframe': videoframe,
      'playlist': playlist,
      'audiosubs': audioSubs
    },

    data () {
      return {
        currentVideo: '',
        subsVisible: false,
        subsCoords: {},
        plCoords: {},
        plVisibility: false
      }
    },
    mounted() {
      this.currentVideo = 'http://localhost:8083/toy4.m4v';
    },
    methods: {
      swapData: function(data) {
        this.currentVideo = data;
      },
      subsAction: function(data) {
        this.subsVisible = data.subs;
        this.subsCoords = { top: data.top, left: data.left};
      },
      playListAction(data) {
        this.plCoords = data;
        this.plVisibility = !this.plVisibility;
      }


    }
  }
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin:0;
  padding: 0;
  background: #000;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

</style>
