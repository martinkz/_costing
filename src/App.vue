<template>
  <v-app>
    <v-main>
      <CostsSection/>
    </v-main>
  </v-app>
</template>

<script>
import HelloWorld from './components/CostsSection';
import CostsSection from './components/CostsSection';

export default {
  name: 'App',

  components: {
    HelloWorld,
    CostsSection,
  },

  data: () => ({
    //
  }),
};
</script>
