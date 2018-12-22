<template>

  <div id="extra-controls" :class="{'extra-controls':true}" v-bind:style="calcCoordinates" ref="subsWrapper">
    <section id="audio" class="extra-controls__section">
      <h3 class="extra_controls__header">Audio</h3>
      <ul>
        <li v-for="(item,idx) in idiomas" data-item-type="idioma"  :class="{selected: item.activo}" @click="selectIdioma">{{ item.idioma }}</li>
      </ul>
    </section>
    <section id="subtitulos" class="extra-controls__section">
      <h3 class="extra_controls__header">Subtítulos</h3>
      <ul>
        <li v-for="(item,idx) in subtitulos" data-item-type="subtitulo" :class="{selected: item.activo}" @click="selectIdioma">{{ item.idioma }}</li>

      </ul>
    </section>

  </div>

</template>

<script>
    import { bus } from '../main';

    export default {
      name: "audioSubs",
      props: {
        visible: {
          type: Boolean
        },
        relCoords: {
          type: Object
        }
      },
      data() {
        return {
          idiomas: [
            {
              idioma: 'inglés',
              activo: true
            },
            {
              idioma: 'español',
              activo :false
            }
          ],
          subtitulos: [
            {
              idioma: 'español',
              activo: true
            },
            {
              idioma: 'inglés[CC]',
              activo :false
            },
            {
              idioma: 'Desactivado',
              activo :false
            }
          ],
          isMounted: false
        }
      },
      computed: {
        calcCoordinates: function() {
          if (this.isMounted) {
            let wrapperCoords = this.$refs.subsWrapper.getBoundingClientRect();
            return {
              top: this.relCoords.top - Math.floor(wrapperCoords.height) + "px",
              left: this.relCoords.left - Math.floor(wrapperCoords.width) + "px"
            }
          }

        }
      },
      mounted() {
        this.isMounted = true
      },
      methods: {
        selectIdioma(e) {
          let type = e.target.dataset.itemType;
          let target = e.target.innerText;

          let collection = (type === "subtitulo") ? this.subtitulos : this.idiomas;

          for (var item of collection) {
            item.activo = (target === item.idioma);
          }
        }
      }
    }
</script>

<style scoped>

  div.extra-controls {
    /*background: rgba(12, 15, 30, 0.8);*/
    background: rgba(100, 100, 100, 0.9);
    color: #fff;
    text-align: left;
    display: none;
    border-radius: 10px;
    padding: 1rem;
  }

  div.extra-controls.active, div.extra-controls.active:hover {
    position: absolute;
    width: 60%;
    grid-template-columns: repeat(2, 1fr);
    display: inline-grid;
  }

  .extra_controls__header {
    margin-left: 2rem;
  }

  .extra-controls__section:first-child {
    border-right: 1px solid rgba(0, 0, 0, .2);
  }

  .extra-controls__section:last-child {
    border-left: 1px solid rgba(255, 255, 255, .1);
  }

  .extra-controls__section ul {
    display: flex;
    flex-flow: column;
    text-align: left;
    margin-left: 1.5rem;
  }

  .extra-controls__section ul li {
    line-height: 150%;
    position: relative;
    cursor: pointer;
  }

  .extra-controls__section ul li:hover {
    color: #16C0B0;
  }

  .extra-controls__section ul li.selected {
    font-weight: 600;
    color: #16C0B0;
  }

  .extra-controls__section ul li.selected::before {
    content: '[*]';
    display: block;
    position: absolute;
    top: 0;
    left: 0;
    transform: translateX(-130%);

  }

</style>
