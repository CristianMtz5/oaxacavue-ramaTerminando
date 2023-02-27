<template>
  <v-app>
    <v-main>
      <CapitalH />
    </v-main>
  </v-app>
</template>

<script>
import CapitalH from "./components/CapitalH.vue";

export default {
  name: "App",
  components: {
    CapitalH,
},
  data: () => ({
    //
  }),
};
</script>