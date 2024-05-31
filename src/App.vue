<template>
  <TopPage v-on:add="addList" v-bind:saved-list="savedList"/>
</template>

<script>
import TopPage from './components/TopPage.vue'

let id = 0;

export default {
  name: 'App',
  components: {
    TopPage
  },
  data() {
    return {
      savedList: []
    }
  },
  methods: {
    addList: function(newList) {
      id = id + 1;
      this.savedList.push({
        id: id,
        column: newList
      });
    }
  }
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
</style>