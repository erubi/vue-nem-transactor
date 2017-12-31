<template>
  <div class="app-ctr">
    <p class="balance">Balance of {{ address }}: {{ balance }}</p>

    <NotifDialogue :notif.sync="notif" />

    <div class="left-ctr">
      <UserInfoForm
         :address="address"
         :privateKey="privateKey"
         :onChangeAddress="onChangeAddress"
         :onChangePrivateKey="onChangePrivateKey"
       />

      <SendXEMForm
         :onSendXEM="onSendXEM"
         :recipientAddress.sync="recipientAddress"
         :xemAmount.sync="xemAmount"
         :message.sync="message"
       />
    </div>

    <div class="right-ctr">
      <TransactionsList
         :recentTransactions="recentTransactions"
       />
    </div>

  </div>
</template>

<script>
import nem from 'nem-sdk';
import UserInfoForm from './UserInfoForm';
import SendXEMForm from './SendXEMForm';
import NotifDialogue from './NotifDialogue';
import TransactionsList from './TransactionsList';

const balanceComma = new Intl.NumberFormat('en-US');
const initData = {
  endpoint: {
    protocol: 'http',
    host: 'https://adamexperimental.cyberblox.my',
    port: 7891,
  },
  notif: null,
  address: 'TCBWQO3WDQ6P45WFLZZJLQ6TVGB5Y45F4VDMM5U6',
  privateKey: '',
  recipientAddress: '',
  xemAmount: null,
  message: '',
  balance: null,
  recentTransactions: [],
};

export default {
  data() {
    const oldData = localStorage.getItem('lux-nem-transactor');
    if (oldData) return Object.assign({}, initData, JSON.parse(oldData));
    return initData;
  },
  updated() {
    // Understand this may not be best for security, just for testing
    const toSave = (({ notif, recentTransactions, ...rest }) => rest)(this.$data);
    localStorage.setItem('lux-nem-transactor', JSON.stringify(toSave));
  },
  components: { UserInfoForm, SendXEMForm, NotifDialogue, TransactionsList },
  methods: {
    onChangeAddress(input) {
      this.connector.close();

      return this.getAccountData(input)
      .then(() => {
        this.address = input;
        this.connectWebSockets();
      })
      .catch(() => {});
    },
    onChangePrivateKey(input) {
      if (!nem.utils.helpers.isPrivateKeyValid(input)) this.notif = { msg: 'Invalid private key', error: true };
      else this.privateKey = input;
    },
    onSendXEM({ recipientAddress, xemAmount, message }) {
      const transferTransaction =
        nem.model.objects.create('transferTransaction')(recipientAddress, xemAmount, message);
      const common = nem.model.objects.create('common')('', this.privateKey);
      const transactionEntity =
        nem.model.transactions.prepare('transferTransaction')(common, transferTransaction, nem.model.network.data.testnet.id);
      const endpoint = nem.model.objects.create('endpoint')(this.endpoint.host, this.endpoint.port);

      // nem errors not always cauught by promise reject, need try/catch
      try {
        return nem.model.transactions.send(common, transactionEntity, endpoint)
        .then((res) => {
          this.recipientAddress = '';
          this.xemAmount = null;
          this.message = '';
          console.log(res);
        })
        .catch((e) => {
          this.notif = { msg: e.message || e.data.message || 'Error sending transaction', error: true };
          return Promise.reject(e);
        });
      } catch (e) {
        this.notif = { msg: e.message || e.data.message || 'Error sending transaction', error: true };
        return Promise.reject(e);
      }
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
        this.notif = { msg: err.data.message, error: true };
        console.log(err);
        throw err;
      });
    },
    connectWebSockets() {
      const endpoint = nem.model.objects.create('endpoint')(nem.model.nodes.defaultTestnet, nem.model.nodes.websocketPort);
      this.connector = nem.com.websockets.connector.create(endpoint, this.address);

      const nemTimeStampToDate = (ts) => {
        const nemEpoch = Date.UTC(2015, 2, 29, 0, 6, 25, 0);
        return new Date(nemEpoch + (ts * 1000));
      };

      const formatTransaction = (t) => {
        let sender = nem.model.address.toAddress(t.signer, -104);
        if (sender === this.address) sender = 'Self';
        let recipient = t.recipient;
        if (recipient === this.address) recipient = 'Self';
        const message = nem.utils.format.hexMessage(t.message);
        const timeStamp = nemTimeStampToDate(t.timeStamp).toUTCString();
        return Object.assign({}, t, { recipient, sender, message, timeStamp });
      };

      this.connector.connect().then(() => {
        nem.com.websockets.subscribe.account.data(this.connector, (res) => {
          this.balance = `${balanceComma.format(res.account.balance / 1000000)} XEM`;
        });

        nem.com.websockets.subscribe.account.transactions.recent(this.connector, (res) => {
          this.recentTransactions = res.data.map(r => formatTransaction(r.transaction));
        });

        nem.com.websockets.subscribe.account.transactions.confirmed(this.connector, (res) => {
          if (res.transaction.recipient === this.address) {
            this.notif = { msg: 'Transaction received (confirmed)' };
            return;
          }
          this.notif = { msg: 'Transaction sent (confirmed)' };

          // Recent transactions subscriptions not updating, getting most recent here
          this.recentTransactions.unshift(formatTransaction(res.transaction));
        });

        nem.com.websockets.subscribe.account.transactions.unconfirmed(this.connector, (res) => {
          if (res.transaction.recipient === this.address) {
            this.notif = { msg: 'Transaction received (unconfirmed)' };
            return;
          }

          this.notif = { msg: 'Sent transaction (unconfirmed)' };
        });

        nem.com.websockets.requests.account.data(this.connector);
        nem.com.websockets.requests.account.transactions.recent(this.connector);
      }, (err) => {
        console.log(err);
      });
    },
  },
  created() {
    this.connectWebSockets();
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.app-ctr{
  width: 1000px;
  height: 100%;
  margin: 0 auto;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  align-content: baseline;
}

.left-ctr {
  flex: 1;
  width: 50%;
  display: inline-block;
}

.right-ctr {
  flex: 1;
  width: 50%;
  display: inline-block;
}

.balance {
  width: 100%;
  margin-bottom: 50px;
}

@media screen and (max-width: 1000px) {
  .app-ctr {
    width: 100%;
  }
  .left-ctr, .right-ctr {
    margin-bottom:20px;
    width: 100%;
  }
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
