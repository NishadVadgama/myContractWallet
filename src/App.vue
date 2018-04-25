<template>
  <div>
    <h1>{{ coinbase }}</h1>
    <label for="balance">Balance</label>
    <h3>{{ balance }}</h3>
    <button v-on:click="initAccount">Get Balance</button>
  </div>
</template>

<script>
import {
  config
} from './config';
const Web3 = require('web3');
// const eth = new Eth(new Eth.HttpProvider('https://ropsten.infura.io'));



export default {
  data() {
    return {
      coinbase: 0x00,
      balance: 0,
      tokens: 0,
      noMetamask: false,
      web3: null,
      token: null,
      intervals: {}
    }
  },
  mounted() {
    if (typeof web3 !== 'undefined') {
      this.web3 = new Web3(web3.currentProvider);
      this.initAccount();
    } else {
      this.noMetamask = true;
    }
    this.initContract();
  },
  methods: {
    initContract: function () {
      this.token = new this.web3.eth.Contract(config.tokenABI, config.contractAddress);
    },
    initAccount: function () {
      this.intervals.iniAccount = setInterval(()=>{
				this.web3.eth.getAccounts((err, result) => {
					if (!err && result[0] != "") {
						this.coinbase = result[0];
						this.getBalance();
					}
				});
			}, 1000);
    },
    getBalance: function () {
      this.web3.eth.getBalance(this.coinbase, (err, bal) => {
				if (!err) {
					this.balance = this.web3.utils.fromWei(bal, "ether");
				} else {
					console.log(err);
				}
			})
    }
  }
}

</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
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
