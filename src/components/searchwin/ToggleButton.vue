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
      :content="content"
      :left="left"
      :top="top"
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
  data: function () {
    return {
      show: false,
      icon: 'search',
      text: '',
      headline: 'Search catalog',
      content: '<h3>Search a catalog</h3> <input type=text><button>Search</button>',
      left: '300px',
      top: '300px'
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
