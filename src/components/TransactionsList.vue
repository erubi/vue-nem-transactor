<template>
  <div>
    <div>
    <h3>Recent Transactions</h3>
    <ul>
      <transition-group name="list">
        <li v-for="t in recentTransactions" :key="t.signature">
          <div>Message: {{ t.message }}</div>
          <div>Amount: {{ t.amount / 1000000 }} XEM</div>
          <div>Fee: {{ t.fee / 1000000 }} XEM</div>
          <div>To: {{ t.recipient }}</div>
          <div>From: {{ t.sender }}</div>
          <div>Date: {{ t.timeStamp }}</div>
        </li>
      </transition-group>
    </ul>
    </div>
  </div>
</template>

<script>
export default {
  props: ['recentTransactions'],
  data() {
    return {};
  },
};
</script>

<style scoped>

div {
  text-align: left;
}

ul {
  height: 500px;
  overflow-y: scroll;
  list-style: none;
  padding: 0;
}

li {
  padding: 5px 0;
  border-bottom: 1px solid gray;
}

.list-item {
  display: inline-block;
  margin-right: 10px;
}
.list-enter-active, .list-leave-active {
  transition: all 1s;
}
.list-enter, .list-leave-to /* .list-leave-active below version 2.1.8 */ {
  opacity: 0;
  transform: translateY(-30px);
}
/*.list-move {*/
  /*transition: transform 1s;*/
/*}*/

</style>
