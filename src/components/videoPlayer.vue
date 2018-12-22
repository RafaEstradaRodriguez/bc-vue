<template>
  <div id="videoframe">

    <div id="videoWrapper">
      <video id="video" ref="video" @timeupdate="updateTime">
        <source v-bind:src="currentVideo">
        Your browser doesn't support the video tag
      </video>

      <div id="controls" @click="videoFunction">

        <div class="controls__play_pause">
          <i :class="playStyle" ref="playCtrl" data-action="Play"></i>
          <i :class="pauseStyle" ref="pauseCtrl" data-action="Pause"></i>
        </div>
        <div class="controls__volume">
          <input id="volumeslider" ref="vsCtrl" type="range" data-action="Volume" min="0" max="100" value="50" step="1"
                 @input="videoFunction">
          <i class="fas fa-volume-down" ref="muteCtrl" data-action="Mute"></i>
        </div>

        <input id="seekslider" ref="seekCtrl" type="range" data-action="Seek" min="0" max="100" value="0" step="1"
               @change="videoFunction">
        <span id="videoTime" ref="videoTime"></span>

        <div class="controls__extra_controls">
          <i class="fas fa-bars"></i>
          <i class="fas fa-step-forward"></i>
          <i class="far fa-list-alt" ref="listCtrl" data-action="Playlist"></i>
          <i class="fas fa-comment-dots" ref="subsCtrl" data-action="Subtítulos"></i>
          <i class="fas fa-expand" ref="fsCtrl" data-action="Fullscreen"></i>
        </div>

      </div>
    </div>

  </div>

</template>

<script>
  import {bus} from '../main';

  export default {
    name: "video-frame",
    props: {
      currentVideo: {
        type: String
      }
    },
    data() {
      return {
        subs: false,
        playStyle: ["fas", "fa-play-circle"],
        pauseStyle: ["fas", "fa-pause-circle", "deactivated"],
        playPauseActive: ""
      }
    },
    mounted: function() {
      window.addEventListener('resize', function() {this.sendCoords});
    },
    beforeDestroy: function() {
      window.removeEventListener('resize', this.sendCoords);
    },
    methods: {
      videoFunction: function (e) {
        let action = e.target.dataset.action;

        switch (action) {
          case "Play": {
            this.$refs.video.play();
            if (this.playPauseActive != "play") {
                this.pauseStyle.pop();
                this.playStyle.push("deactivated");
                this.playPauseActive = "play";
            }
            break;
          }

          case "Pause": {
            if (this.playPauseActive != "pause") {
                this.pauseStyle.push("deactivated");
                this.playStyle.pop();
                this.playPauseActive = "pause";
            }
            this.subs = !this.subs;
            this.$refs.video.pause();
            break;
          }

          case "Subtítulos": {
            this.subs = !this.subs;
            this.sendCoords();
            break;
          }

          case "Playlist": {
            let coords = this.$refs.listCtrl.getBoundingClientRect();
            let video = this.$refs.video.getBoundingClientRect();

            let res = {
              top: coords.top,
              left: coords.right,
              width: video.width,
              height: video.height
            };

            console.log("RES: ", res);

            this.$emit('PlaylistAction', res);
            break;
          }

          case "Fullscreen": {
            if (this.$refs.video.requestFullScreen) {
              this.$refs.video.requestFullScreen();
            } else if (this.$refs.video.webkitRequestFullScreen) {
              this.$refs.video.webkitRequestFullScreen();
            } else if (this.$refs.video.mozRequestFullScreen) {
              this.$refs.video.mozRequestFullScreen();
            }
            break;
          }

          case "Volume": {
            this.$refs.video.volume = this.$refs.vsCtrl.value / 100
            break;
          }

          case "Seek": {
            this.$refs.video.currentTime = this.$refs.video.duration * (this.$refs.seekCtrl.value / 100);
            break;
          }

          case "Mute": {
            if (this.$refs.muteCtrl.classList.contains("fa-volume-down")) {
              this.$refs.muteCtrl.classList.replace("fa-volume-down", "fa-volume-mute");
              this.$refs.video.muted = true;
            } else {
              this.$refs.muteCtrl.classList.replace("fa-volume-mute", "fa-volume-down");
              this.$refs.video.muted = false;
            }
            break;
          }
        }
      },
      sendCoords() {
        let resCoords = this.$refs.subsCtrl.getBoundingClientRect();
        this.$emit('subsAction', {
          subs: this.subs,
          top: resCoords.top,
          left: resCoords.right
        });
      },
      updateTime: function (e) {
        let date = new Date(null);
        date.setSeconds(this.$refs.video.currentTime);
        this.$refs.videoTime.innerText = date.toISOString().substr(11, 8);

        this.$refs.seekCtrl.value = this.$refs.video.currentTime;
      }
    },
    created() {

      bus.$on('reload', () => {
        this.$refs.video.load();
        this.$refs.video.play();
      })
    }
  }
</script>

<style scoped>
  #videoWrapper {
    position: relative;
    padding-bottom: 56.25%; /* 16:9 */
    height: 0;
    overflow: hidden;
  }

  #videoWrapper video {
    position: absolute;
    width: 100%;
    height: 100%;
    top:0;
    left:0;
  }

  #controls {
    display: flex;
    position: absolute;
    bottom: 10%;
    left: 2%;
    width: 96%;
  }

  .controls__play_pause > i.deactivated {
    display: none;
  }

  .controls__volume {
    display: flex;
    flex-flow: column nowrap;
    transition: 0.3s;
  }

  .controls__volume:hover input[type=range]#volumeslider {
    transform: translate(-10px, -200%) rotate(270deg) scaleX(1);
  }

  button {
    background: url(../assets/pause.svg) no-repeat;
    background: none;
  }

  i {
    font-size: 2.2rem;
    cursor: pointer;
    color: #fff;
    padding: .5rem;
    transition: .2s;
  }

  i:hover {
    color: #16C0B0;
    transform:scale(1.3);
  }

  input[type=range] {
    cursor: pointer;
  }

  input[type=range]#volumeslider {
    transition: .3s;
    transform: translate(-10px, -200%) rotate(270deg) scaleX(0);
    width: 4rem;
    position: absolute;
  }

  input[type=range]:focus {
    outline: none;
  }

  input[type="range"] {
    margin: auto;
    -webkit-appearance: none;
    position: relative;
    overflow: hidden;
    height: 20px;
    width: 200px;
    cursor: pointer;
    border-radius: 0; /* iOS */
  }

  ::-webkit-slider-runnable-track {
    background: #042320;
  }

  /*
   * 1. Set to 0 width and remove border for a slider without a thumb
   */
  ::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 20px; /* 1 */
    height: 20px;
    border-radius: 3px;
    background: #39f7f9;
    box-shadow: -100vw 0 0 100vw #16C0B0;
    border: 2px solid #0a5850; /* 1 */
  }

  ::-moz-range-track {
    height: 20px;
    background: #042320;
  }

  ::-moz-range-thumb {
    background: #39f7f9;
    height: 20px;
    width: 20px;
    border-radius: 20px;
    border: 3px solid #0a5850;
    border-radius: 0 !important;
    box-shadow: -100vw 0 0 100vw #16C0B0;
    box-sizing: border-box;
  }

  ::-ms-fill-lower {
    background: #16C0B0;
  }

  ::-ms-thumb {
    background: #39f7f9;
    border: 2px solid #0a5850;
    height: 20px;
    width: 20px;
    border-radius: 20px;
    box-sizing: border-box;
  }

  ::-ms-ticks-after {
    display: none;
  }

  ::-ms-ticks-before {
    display: none;
  }

  ::-ms-track {
    background: #042320;
    color: transparent;
    height: 20px;
    border: none;
  }

  ::-ms-tooltip {
    display: none;
  }

  .controls__extra_controls {
    margin-left: auto;
  }

  .controls__extra_controls i:first-child {
    display: none;
  }


  @media screen and (max-width: 600px) {
    i {
      font-size: 1.5rem;
    }

    .controls__extra_controls {
      display: flex;
      flex-flow: column;
      position: absolute;
      bottom: 0%;
      right: 0%;
    }



    .controls__extra_controls i:nth-child(n+2) {
      display: none;
    }

    .controls__extra_controls:hover i:nth-child(n+2) {
      display: block;
    }

    .controls__extra_controls i:first-child {
      display: block;
      order:1;
    }


  }

</style>
