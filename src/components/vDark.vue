<template>
  <div class="dark">
    <label class="dark-checkbox">
      <input type="checkbox" v-model="dark" />
      <span :class="{ dark: dark }"></span>
    </label>
  </div>
</template>

<script>
export default {
  data() {
    return {
      dark: null,
    };
  },
  mounted() {
    if (localStorage.dark === "false" || !localStorage.dark) {
      this.dark = false;
    } else {
      this.dark = true;
    }
  },
  watch: {
    dark(newDark) {
      localStorage.dark = newDark;
      this.$emit("upDark");
    },
  },
};
</script>


