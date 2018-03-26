<template>

  <div class="">

    <v-btn icon @click="toggleUi">
      <v-icon medium>{{icon}}</v-icon>
      {{text}}
    </v-btn>

    <wgu-searchwin
      ref="searchwin"
      :icon="icon"
      :headline="headline"
      :left="left"
      :top="top"
      :mode="mode"
      :catalog="catalog"
      :version="version"
    />

  </div>

</template>

<script>

import searchwin from './searchwin'

export default {
  name: 'wgu-toggle-searchwin-button',
  components: {
    'wgu-searchwin': searchwin
  },
  props: ['catalog', 'mode', 'version'],
  data: function () {
    return {
      show: false,
      icon: 'search',
      text: '',
      headline: 'Search',
      left: '100px',
      top: '200px'
    }
  },
  methods: {
    toggleUi () {
      this.show = !this.show;
      // forward property 'show' to window child componnet
      this.$refs.searchwin.show = this.show;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>

</style>
