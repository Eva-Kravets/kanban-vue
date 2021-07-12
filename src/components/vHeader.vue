<template>
  <header class="header">
    <div class="container">
      <div class="header-wrapper">
        <a href="#"><img src="../assets/logo.svg" alt="logo"/></a>
        <h1 class="header-title">{{ nameProject }}</h1>
        <v-dark @upDark="toup()" />
      </div>
    </div>
  </header>
</template>

<script>
import vDark from "./vDark";
export default {
  data() {
    return {
      nameProject: "Канбан-доска на Vue.js",
      k: null,
    };
  },
  components: {
    vDark,
  },
  methods: {
    toup() {
      this.k = localStorage.dark;
      this.$emit("topDark");
    },
  },
};
</script>

