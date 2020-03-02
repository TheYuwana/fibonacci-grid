<template>
  <div id="app">
    <grid />
  </div>
</template>

<script>
import Grid from './components/Grid.vue'

export default {
  name: 'App',
  components: {
    Grid
  }
}
</script>

<style lang="scss">
@import "@/styles/general";
</style>
