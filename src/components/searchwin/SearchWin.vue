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
          Search for <input type="text" style="border:1px solid #ccc" v-model="q"/>
          <p>Displaying {{ results.length }} results,
          filtered by <strong>{{ q }}</strong></p>
            <div v-for="res in results">
              <div v-if="res.link" style="float:right">
                <span v-for="link in res.link">
                  <a target="_blank"
                    :href="link.split('|')[2]+'?&request=getcapabilities'"
                    v-if="link.split('|')[3]==='OGC:WMS'">
                      {{link.split('|')[0].substring(0,20)}}</a>
                </span>
              </div>
              <strong>{{res.title||res.defaultTitle}}</strong><br/>
              {{res.abstract.substring(0,80)}}...
            </div>

        </div>
      </div>
    </v-card-title>

    <v-card-actions>

    </v-card-actions>
  </v-card>

</template>

<script>
  import { DraggableWin } from '../../directives/DraggableWin.js';

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
    }
  }
</script>

<style>

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
