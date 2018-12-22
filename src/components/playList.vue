<template>
  <div id="playlist" :class="visibilityStyle" ref="playListWrapper" v-bind:style="calcCoordinates">
    <h2>Temporada 11</h2>

    <ul ref="playlist" class="playlist__list" @click="changeSource">
      <!--<li class="video" url-value="http://localhost:8083/robin.ogg">Robin Hood</li>
      <li class="video" url-value="http://localhost:8083/bohemian.m4v">Bohemian Rapsody</li>-->
      <li class="video" url-value="http://localhost:8083/toy4.m4v">Toy Story 4</li>
      <li class="video" url-value="http://localhost:8083/grinch.m4v">Grinch</li>
      <li class="video" url-value="http://localhost:8083/big_buck_bunny.mp4">Big Buck Bunny</li>
    </ul>

  </div>
</template>

<script>
    import { bus } from '../main';

    export default {
      name: "playlist",
      props: {
        currentVideo: {
          type: String
        },
        relCoords: {
          type: Object
        },
        visible: {
          type: Boolean
        }
      },
      data() {
        return {
          isMounted: false
        }
      },
      methods: {
        changeSource: function(e) {
          let videoValue = e.target.attributes[1].value;
          this.$emit('updatedData', videoValue);
          bus.$emit('reload');
        }
      },
      mounted() {
        this.isMounted = true
      },
      computed: {
        calcCoordinates: function() {
          if (this.isMounted) {
            let wrapperCoords = this.$refs.playListWrapper.getBoundingClientRect();


            let res = {
              position: "absolute",
              top: (this.relCoords.top - (this.relCoords.height * 0.5)) + "px",
              left: (this.relCoords.left - (this.relCoords.width * 0.5)) + "px"
            };
            return res;
          }
        },
        visibilityStyle() {
          console.log("Visibility.....");
          let style = ['playlist'];
          if (this.visible) {
            style.push('active');
          }
          return style;
        }
      }
    }
</script>

<style scoped>

  .playlist {
    border-radius: 6px;
    background-color: #0C0F1E;
    color: #FFF;
    padding: 0;
    margin: 0;
    display: none;
    position: absolute;
    width: 50%;
    height: 30%;
    overflow: scroll;
  }

  .playlist.active {
    display: block;
  }

  h2 {
    text-align: left;
    padding: 1rem;
  }

  .playlist__list {
    display: flex;
    flex-flow: column;
    text-align: left;
    margin: 0;
    padding: 0 0 2rem 0 ;

  }

  .playlist__list li {
    height: 2.5rem;
    display: inline-block;
    cursor: pointer;
    padding-left: 1rem;
    line-height: 2.5rem;
  }

  .playlist__list li:hover {
    color:#16C0B0;
    background: rgba(255, 255, 255, .1);
  }

  @media screen and (max-width:600px) {
    .playlist {
      position: absolute;
      left: 2%;
      top: 2%;
    }
  }

</style>
