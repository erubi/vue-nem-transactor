<template>
  <div v-if="proxyError" v-on:click="proxyError = null">
      {{ proxyError }}
    </div>
</template>

<script>
export default {
  props: ['error'],
  data() {
    return {
    };
  },
  updated() {
    this.timeout = setTimeout(() => {
      this.proxyError = null;
    }, 5000);
  },
  computed: {
    proxyError: {
      get() { return this.error; },
      set(newValue) { this.$emit('update:error', newValue); },
    },
  },
};
</script>

<style scoped>
div {
  position: absolute;
  top: 10%;
  right: 10%;
  height: 50px;
  line-height: 50px;
  background: red;
  color: white;
  padding: 5px 10px;
  border-radius: 6px;
}
</style>
