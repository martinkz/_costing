<template>
  <v-app>
    <v-main>
      <CostsSection/>
    </v-main>
  </v-app>
</template>

<script>
import CostsSection from './components/CostsSection';

export default {
  name: 'App',

  components: {
    CostsSection,
  },

  data: () => ({
    //
  }),
};
</script>

<style scoped>
  .v-application {
    background: #dddddd !important;
  }
</style>