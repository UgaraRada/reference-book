<template>
  <v-app>
    <v-main>
      <reference-book />
    </v-main>
  </v-app>
</template>

<script>

import ReferenceBook from './components/ReferenceBook.vue';

export default {
  name: 'App',

  components: {
    ReferenceBook,
  },
};

</script>
