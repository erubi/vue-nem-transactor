<template>
  <div>
    <form>
      <h3>Send XEM</h3>
      <div>
        <label for="recipient-address">Recipient Address</label>
        <input v-model="recipientAddress" id="recipient-address"/>
      </div>

      <div>
        <label for="xem-amount">Amount (XEM)</label>
        <input v-model="xemAmount" id="xem-amount" type="number"/>
      </div>

      <button @click.prevent="onSendXEM({ recipientAddress, xemAmount, message })">Submit</button>
    </form>
  </div>
</template>

<script>
export default {
  props: ['onSendXEM'],
  data() {
    return {
      recipientAddress: '',
      xemAmount: null,
      message: '',
    };
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
