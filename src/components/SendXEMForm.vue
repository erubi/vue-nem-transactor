<template>
  <div>
    <form>
      <h3>Send XEM</h3>
      <div>
        <label for="recipient-address">Recipient Address</label>
        <input v-model="proxyRecipientAddress" id="recipient-address"/>
      </div>

      <div>
        <label for="xem-amount">Amount (XEM)</label>
        <input v-model="proxyXEMAmount" id="xem-amount" type="number"/>
      </div>

      <div>
        <label for="message">Message</label>
        <input v-model="proxyMessage" id="message"/>
      </div>

      <button @click.prevent="onSendXEM({ recipientAddress, xemAmount, message })">Submit</button>
    </form>
  </div>
</template>

<script>
export default {
  props: ['onSendXEM', 'xemAmount', 'recipientAddress', 'message'],
  data() {
    return { };
  },
  computed: {
    proxyRecipientAddress: {
      get() { return this.recipientAddress; },
      set(newValue) { this.$emit('update:recipientAddress', newValue); },
    },
    proxyXEMAmount: {
      get() { return this.xemAmount; },
      set(newValue) { this.$emit('update:xemAmount', newValue); },
    },
    proxyMessage: {
      get() { return this.message; },
      set(newValue) { this.$emit('update:message', newValue); },
    },
  },
};
</script>

<style scoped>

form {
  text-align: left;
  width: 400px;
  float: left;
}

input {
  width: 100%;
}

</style>
