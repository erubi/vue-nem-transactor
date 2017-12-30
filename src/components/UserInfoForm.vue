<template>
  <div class="clearfix">
    <form>
      <h3>User Info</h3>
      <div class="input-ctr">
      <label for="address-input">Address</label>
      <span v-if="address && address === addressInput">&#10004;</span>
      <input v-model="addressInput" :placeholder="address" id="address-input"/>
      </div>

      <div class="input-ctr">
      <label for="private-key">Private Key</label>
      <span v-if="privateKey && privateKey === privateKeyInput">&#10004;</span>
      <input v-model="privateKeyInput" id="private-key" type="password"/>
      </div>

      <button @click.prevent="onSubmit">Submit</button>
    </form>
  </div>
</template>

<script>
export default {
  props: ['address', 'privateKey', 'onChangeAddress', 'changeAddress', 'onChangePrivateKey', 'validKeyPair'],
  data() {
    return {
      addressInput: this.address,
      privateKeyInput: this.privateKey,
    };
  },
  methods: {
    onSubmit() {
      this.addressInput = this.addressInput.replace(/-/gi, '');
      if (this.address !== this.addressInput) {
        this.onChangeAddress(this.addressInput)
        .then(() => {
          this.onChangePrivateKey(this.privateKeyInput);
        })
        .catch(() => {});
      } else {
        this.onChangePrivateKey(this.privateKeyInput);
      }
    },
  },
};
</script>

<style scoped>

.clearfix{
 content: "";
  display: table;
  clear: both;
}

form {
  text-align: left;
  width: 400px;
  float: left;
}

input {
  width: 100%;
}

</style>
