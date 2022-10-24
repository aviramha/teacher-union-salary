<template>
  <v-app>
    <v-main>
      <Simulator/>
    </v-main>
  </v-app>
</template>

<script>
import Simulator from './components/Simulator';

export default {
  name: 'App',

  components: {
    Simulator,
  },

  data: () => ({
    //
  }),
};
</script>
