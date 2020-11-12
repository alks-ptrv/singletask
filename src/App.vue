<template>
  <v-app>
    <MainCard />
  </v-app>
</template>

<script>
import MainCard from "./components/MainCard";

export default {
  name: "App",

  components: {
    MainCard,
  },

  data: () => ({
    //
  }),
};
</script>
