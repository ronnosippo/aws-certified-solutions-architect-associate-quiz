<template>
  <v-ons-navigator :page-stack="pageStack" >
    <component :is="page" v-for="page in pageStack" :key="page.key" :page-stack="pageStack"></component>
  </v-ons-navigator>
</template>

<script>
  import topPage from './TopPage';

  export default {
    data() {
      return {
        pageStack: [topPage]
      }
    }
  }
</script>
