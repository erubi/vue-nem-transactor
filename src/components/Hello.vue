<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>Balance of {{ address }}: {{ balance }}</p>
  </div>
</template>

<script>
import nem from 'nem-sdk';

const balanceComma = new Intl.NumberFormat('en-US');

export default {
  data() {
    return {
      msg: 'Welcome to Your Vue.js App',
      address: 'TCBWQO3WDQ6P45WFLZZJLQ6TVGB5Y45F4VDMM5U6',
      endpoint: {
        protocol: 'http',
        host: 'https://adamexperimental.cyberblox.my',
        port: 7891,
      },
      balance: null,
    };
  },
  methods: {
    getAccountData() {
      nem.com.requests.account.data(
        this.endpoint,
        this.address,
      ).then((res) => {
        console.log(res);
        this.balance = `${balanceComma.format(res.account.balance / 1000000)} XEM`;
      }, (err) => {
        console.log(err);
      });
    },
  },
  created() {
    this.getAccountData();
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
