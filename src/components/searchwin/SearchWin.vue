<template>

  <v-card v-draggable-win class="wgu-searchwin" v-if=show v-bind:style="{ left: left, top: top }">
    <v-toolbar class="red darken-3 white--text" dark>
      <v-toolbar-side-icon><v-icon>{{icon}}</v-icon></v-toolbar-side-icon>
      <v-toolbar-title>Search</v-toolbar-title>
      <v-spacer></v-spacer>
    </v-toolbar>

    <v-card-title primary-title>
      <div>
        <div class="">
          Search for <input type="text" class="wgu-input" v-model="q"/>
          <p>Displaying {{ results.length }} results,
          filtered by <strong>{{ q }}</strong></p>
          <div class="wgu-results">
            <div v-for="res in results" class="wgu-res">
              <img src="static/shim.gif" :style="'background-image: url('+res.image.split('|')[1]||res.image[0].split('|')[1]+');'" v-if="res.image" class="wgu-res-image" />
              <div v-if="res.link" style="float:right">
                <span v-for="link in res.link">
                 <button class="btn"
                  v-if="link.split('|')[3]==='OGC:WMS'"
                  v-on:click="set(link.split('|')[2],link.split('|')[0])">{{link.split('|')[0]}}</button>
                </span>
              </div>
              <strong>{{res.title||res.defaultTitle}}</strong><br/>
              {{res.abstract.substring(0,80)}}...
            </div>
          </div>
        </div>
      </div>
    </v-card-title>

    <v-card-actions>

    </v-card-actions>
  </v-card>

</template>

<script>
  import { WguEventBus } from '../../WguEventBus.js'
  import { DraggableWin } from '../../directives/DraggableWin.js';
  import TileWMSSource from 'ol/source/TileWMS'
  import TileLayer from 'ol/layer/Tile'

  export default {
    directives: {
      DraggableWin
    },
    props: ['icon', 'headline', 'content'],
    data () {
      return {
        show: false,
        left: '300px',
        top: '300px',
        results: [],
        q: ''
      }
    },
    created () {
      var me = this;
      // Listen to the ol-map-mounted event and receive the OL map instance
      WguEventBus.$on('ol-map-mounted', (olMap) => {
        // make the OL map accesible in this component
        me.map = olMap;
      });
    },
    watch: {
      q (nw, old) {
        var me = this;
        if (nw !== '') {
          fetch('//nationaalgeoregister.nl/geonetwork/srv/dut/q?_content_type=json&fast=index&from=1&resultType=details&sortBy=relevance&to=20&dynamic&any=' + nw).then(
            function (response) {
              return response.text();
            }).then(
              function (text) {
                me.results = JSON.parse(text).metadata || [];
              });
        } else {
          me.results = [];
        }
      }
    },
    methods: {
      /**
       * Creates a vector layer for the measurement results and adds it to the
       * map.
       */
      set (url, layer) {
        var me = this;
        var l = new TileLayer({
          source: new TileWMSSource({
            url: url,
            params: {'LAYERS': layer, 'TILED': true},
            serverType: 'geoserver',
            transition: 0
          })
        })
        me.map.addLayer(l);
      }
    }
  }
</script>

<style>

  .btn {
    padding: 5px;
    width: 60px;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: clip;
  }

  .wgu-res {
    clear: both;
    margin-bottom: 10px;
  }

  .wgu-res-image {
    background-size:     cover;
    background-repeat:   no-repeat;
    background-position: center center;
    width:60px;
    height:60px;
    border-radius: 50%;
    float:left;
    margin-right: 10px;
  }

  .wgu-results {
    overflow: scroll;
    max-width: 500px;
    max-height: 300px;
  }

  .wgu-input {
    border: 1px solid #ccc;
  }

  .wgu-searchwin {
    background-color: white;
    z-index: 2;
  }

  .wgu-searchwin.card {
    position: absolute;
  }

  .wgu-searchwin .info-link {
    padding-left: 15px;
  }

</style>
