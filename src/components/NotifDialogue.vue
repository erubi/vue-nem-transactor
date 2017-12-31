<template>
  <transition name="fade">
    <div v-if="proxyNotif" v-on:click="proxyNotif = null" :class="{ error: proxyNotif.error }">
      {{ proxyNotif.msg }}
    </div>
  </transition>
</template>

<script>
export default {
  props: ['notif'],
  data() {
    return {
    };
  },
  updated() {
    if (this.timeout) clearTimeout(this.timeout);
    this.timeout = setTimeout(() => {
      this.proxyNotif = null;
    }, 5000);
  },
  computed: {
    proxyNotif: {
      get() { return this.notif; },
      set(newValue) { this.$emit('update:notif', newValue); },
    },
  },
};
</script>

<style scoped>
div {
  position: fixed;
  top: 60px;
  right: 50px;
  height: 50px;
  line-height: 50px;
  background: white;
  color: white;
  padding: 5px 10px;
  border-radius: 6px;
  background: green;
}

.error {
  background: red;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0
}

</style>
