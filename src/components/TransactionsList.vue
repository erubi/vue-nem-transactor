<template>
  <div>
    <div>
    <h3>Sent Transactions</h3>
    <ul>
      <li v-for="t in outgoingTransactions">
        <div>Amount: {{ t.transaction.amount / 1000000 }}</div>
        <div>Fee: {{ t.transaction.fee / 1000000 }}</div>
        <div>To: {{ t.transaction.recipient }}</div>
        <div>Date: {{ t.transaction.timeStamp }}</div>
      </li>
    </ul>
    </div>

    <div>
    <h3>Received Transactions</h3>
    <ul>
      <li v-for="t in incomingTransactions">
        <div>Amount: {{ t.transaction.amount / 1000000 }}</div>
        <div>Fee: {{ t.transaction.fee / 1000000 }}</div>
        <div>From: {{ t.transaction.sender }}</div>
        <div>Date: {{ t.transaction.timeStamp }}</div>
      </li>
    </ul>
    </div>
  </div>
</template>

<script>
export default {
  props: ['fetchIncomingTransactions', 'fetchOutgoingTransactions'],
  data() {
    return {
      incomingTransactions: [],
      outgoingTransactions: [],
    };
  },
  mounted() {
    this.fetchIncomingTransactions()
    .then((res) => {
      this.incomingTransactions = res;
    });

    this.fetchOutgoingTransactions()
    .then((res) => {
      this.outgoingTransactions = res;
    });
  },
};
</script>

<style scoped>

ul {
  height: 200px;
  overflow-y: scroll;
  list-style: none;
  text-align: left
}

</style>
