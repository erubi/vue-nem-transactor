<template>
  <div class="app-ctr">
    <p class="balance">Balance of {{ address }}: {{ balance }}</p>

    <ErrorDialogue :error.sync="error" />

    <div class="left-ctr">
      <UserInfoForm
         :address="address"
         :privateKey="privateKey"
         :onChangeAddress="onChangeAddress"
         :onChangePrivateKey="onChangePrivateKey"
         />

      <SendXEMForm
         :onSendXEM="onSendXEM"
      />
    </div>

    <TransactionsList />

  </div>
</template>

<script>
import nem from 'nem-sdk';
import UserInfoForm from './UserInfoForm';
import SendXEMForm from './SendXEMForm';
import ErrorDialogue from './ErrorDialogue';
import TransactionsList from './TransactionsList';

const balanceComma = new Intl.NumberFormat('en-US');
const initData = {
  endpoint: {
    protocol: 'http',
    host: 'https://adamexperimental.cyberblox.my',
    port: 7891,
  },
  error: '',
  address: 'TCBWQO3WDQ6P45WFLZZJLQ6TVGB5Y45F4VDMM5U6',
  privateKey: '',
  recipientAddress: '',
  keyPair: null,
  balance: null,
};

export default {
  data() {
    const oldData = localStorage.getItem('lux-nem-transactor');
    if (oldData) return Object.assign({}, initData, JSON.parse(oldData));
    return initData;
  },
  updated() {
    localStorage.setItem('lux-nem-transactor', JSON.stringify(this.$data));
  },
  components: { UserInfoForm, SendXEMForm, ErrorDialogue, TransactionsList },
  methods: {
    onChangeAddress(input) {
      this.getAccountData(input)
      .then(() => {
        this.address = input;
      })
      .catch((err) => {
        this.error = err.data.message;
        throw (err);
      });
    },
    onChangePrivateKey(input) {
      try {
        this.keyPair = nem.crypto.keyPair.create(input);
      } catch (e) {
        this.keyPair = null;
        this.error = 'Invalid private key';
      }
      this.privateKey = input;
    },
    onSendXEM({ recipientAddress, xemAmount, message = 'Test' }) {
      const transferTransaction =
        nem.model.objects.create('transferTransaction')(recipientAddress, xemAmount, message);
      const common = nem.model.objects.create('common')('', this.privateKey);
      const transactionEntity =
        nem.model.transactions.prepare('transferTransaction')(common, transferTransaction, nem.model.network.data.testnet.id);
      const endpoint = nem.model.objects.create('endpoint')(this.$data.endpoint.host, this.$data.endpoint.port);

      nem.model.transactions.send(common, transactionEntity, endpoint)
      .then((res) => {
        console.log(res);
      })
      .catch((err) => {
        this.error = 'Failed transaction';
      });
    },
    getAccountData(address = this.address) {
      return nem.com.requests.account.data(
        this.endpoint,
        address,
      ).then((res) => {
        console.log(res);
        this.balance = `${balanceComma.format(res.account.balance / 1000000)} XEM`;
      })
      .catch((err) => {
        console.log(err);
        throw err;
      });
    },
  },
  created() {
    this.getAccountData()
    .catch(() => {});
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.app-ctr{
  width: 1000px;
  height: 100%;
  margin: 0 auto;
}

.left-ctr {
  width: 50%;
  display: inline-block;
  float: left;
}

.balance {
  margin-bottom: 50px;
}
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
