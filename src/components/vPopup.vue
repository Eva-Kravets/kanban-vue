<template>
  <div class="popup"  ref="popup">
      <slot></slot>
  </div>
</template>

<script>
export default {
  mounted() {
    var vm = this;
    document.addEventListener("click", function(item) {
      if (item.path[0] ===vm.$refs.popup) vm.$emit('close');
    });
  },
};
</script>

