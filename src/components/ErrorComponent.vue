<template>
  <v-card>
    <v-card-title>
      No Dejar Campos Vacios
    </v-card-title>
    <v-card-actions>
      <v-spacer></v-spacer>
      <v-btn color="error darken-1" text @click="closeError">
        Entiendo
      </v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
export default {
  name: "ErrorComponent",
  data() {
    return {
      // dialog: true,
    };
  },
  methods: {
    closeError() {
      this.$emit("closeErrorComp");
    }
  }
};
</script>
