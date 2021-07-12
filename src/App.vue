<template>
<div class="app" ></div>
  <v-header :data-dark="mode" @topDark="a"/>
  <v-main :data-dark="mode"/>
  <v-footer/>
</template>

<script>
import VHeader from "./components/vHeader.vue";
import vMain from "./components/vMain.vue";
import vFooter from "./components/vFooter.vue";

export default {
  name: "App",
  data() {
    return {
      mode: localStorage.dark,
    };
  },
  methods:{
    a(){
      this.mode = localStorage.dark;
    }
  },
  components: {
    VHeader,
    vMain,
    vFooter
  },
};
</script>


