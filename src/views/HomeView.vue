<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
    <div v-if="hasMetaMask">
      <button v-if="!isConnected" @click="connectWallet" type="button"> Connect to MetaMask</button>
      <div v-if="isConnected">
        <h4>Account {{account}}</h4>
          <button @click="HelloWorldMessage"> Run hello world contract </button>  
          <button @click="getFavoriteNumbmer"> Get Favorite Number</button>
          <br/>
          <br/>
          <input type="number" v-model.number="value">
          <button @click="addToFavoriteNumbmer"> Add to Favorite Number</button>
          <button @click="multiplyFavoriteNumbmer"> Multiply Favorite Number</button>
          <h3 v-if="result">{{result}}</h3>
      </div>
      
    </div>
    <div v-else>
      Please insall <a href="http://metamask.io/">MetaMask</a>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
import ContractABI from '../contract/HelloWorldContract.json'
import Web3 from 'web3/dist/web3.min.js'



export default {
  name: 'HomeView',
  components: {
    HelloWorld
  },
  data () {
    return {
      hasMetaMask: false,
      isConnected: false,
      contractAbi: ContractABI,
      result: null,
      value: null,
      web3: null,
      account: ''
    }
  },
  methods: {
    async connectWallet(){
      console.log("connecting wallet...")
      const accounts = await window.ethereum.request({ method: 'eth_requestAccounts' })
      this.account = accounts[0]
      this.isConnected = true

      this.web3 = new Web3(window.ethereum)
      this.web3.eth.defaultAccount = this.account


    },

    async HelloWorldMessage(){
      // console.log(" Hi i am hero")
      let helloWorldContract = new this.web3.eth.Contract(this.contractAbi, '0xf7E7A3e94171B7de1C620B596858e0Edbd70A481')
      this.result = await helloWorldContract.methods.sayHello().call()
    },

    async getFavoriteNumbmer(){
        let helloWorldContract = new this.web3.eth.Contract(this.contractAbi, '0xf7E7A3e94171B7de1C620B596858e0Edbd70A481')
        this.result = await helloWorldContract.methods.viewSepcialNumber().call()
    },

    async addToFavoriteNumbmer(){
      let helloWorldContract = new this.web3.eth.Contract(this.contractAbi, '0xf7E7A3e94171B7de1C620B596858e0Edbd70A481')
      if (!this.value){
        return
      }
      helloWorldContract.methods.addToSpecialNumber(this.value).send({'from': this.account}).on('receipt', function(){
        console.log("Number has been added to special number")
      })
    },

    async multiplyFavoriteNumbmer(){
      let helloWorldContract = new this.web3.eth.Contract(this.contractAbi, '0xf7E7A3e94171B7de1C620B596858e0Edbd70A481')
      if (!this.value){
        return
      }
      helloWorldContract.methods.multiplySpecialNumber(this.value).send({'from': this.account}).on('receipt', function(){
        console.log("Special number has been multiplied")
      })
    }
  },

  mounted() {
    if (typeof window.ethereum !== 'undefined') {
        this.hasMetaMask = true
    }
  }
}
</script>
