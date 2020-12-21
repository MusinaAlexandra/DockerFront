<template>
  <div id="app">
    <router-view id="body"/>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';

@Component({
  components: {
  },
})
export default class App extends Vue {}
</script>

<style lang="scss">
#app {

  font-family: Calibri, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  height: 100%;
  width: 100%;
  position: absolute;
  top: 0;
  left: 0;
}

#body {
  grid-area: body;
  height: 100%;
  width: 100%;
  position: absolute;
  top: 0;
  left: 0;
  background-color: gray;
}
</style>
