<template >
  <div class="container">
        <div class="row">
          <div class="col-sm-12">
            <h2 class="title my-16"><span>The Etho Protocol</span> Blockchain Bridge</h2>
  <v-card
    max-width="600"
    class="my-16 px-4 py-10"
  >
    <v-tabs
      background-color="#840032"
      center-active
      dark
      class="pb-3"
    >
      <v-tab @click="showEthBridge=true, showBscBridge=false">ETH</v-tab>
      <v-tab @click="showEthBridge=false, showBscBridge=true">BSC</v-tab>

      

    </v-tabs>
    <EthBridge v-if="showEthBridge"></EthBridge>
    <BscBridge v-if="showBscBridge"></BscBridge>
  </v-card>
  </div>
  </div>
  </div>
</template>
<script>
/* eslint-disable camelcase */
//import Vue from 'vue'
import EthBridge from "./ethbridge";
import BscBridge from "./bscbridge";
import { Contract, providers, BigNumber } from 'ethers'
import Axios from 'axios'
import firebase from 'firebase/app'
import 'firebase/firestore'
import BridgeAssistO from '/abis/BridgeAssistO.json'
import Token from '/abis/Token.json'
if (!firebase.apps.length)
  firebase.initializeApp({
    apiKey: 'AIzaSyCNsZTX7t6dxYlPSbw3AA_0M08C8wXxgwk',
    authDomain: 'ethobridge.firebaseapp.com',
    projectId: 'ethobridge',
    storageBucket: 'ethobridge.appspot.com',
    messagingSenderId: '299191786261',
    appId: '1:299191786261:web:41643af0eb7df59a8ba71c',
  })
const db = firebase.firestore()
const address_BAO = '0x2edFef4716612b705993C73e69728bEB6E28c57F'
const address_TKN = '0x99676c9fa4c77848aeb2383fcfbd7e980dc25027'

const address_BA = '0x22CcD028d21D3A3e64646493a223b20cB22C6dF0'
const providerO = new providers.JsonRpcProvider('https://rpc.ether1.org')

const provider_MAINNET = new providers.InfuraProvider(1, '36aca5d4749c4d0fb9352bb93c435b02')
const TKN = new Contract(address_TKN, Token.abi, provider_MAINNET)

function removeTrailingZeros(str) {
  if (str === '0') return str
  if (str.slice(-1) === '0') return removeTrailingZeros(str.substr(0, str.length - 1))
  if (str.slice(-1) === '.') return str.substr(0, str.length - 1)
  return str
}
function numstrToBN(input) {
  const spl = input.split('.')
  if (spl[1]) spl[1] = spl[1].substr(0, 18)
  return BigNumber.from(spl.join('') + '000000000000000000'.substr(0, 18 - (spl[1] || '').length))
}
function BNStrToNumstr(str, precision = 3) {
  if (str === '0') return str
  if (isNaN(Number(str))) return 'NaN'
  if (str.length <= 18) return removeTrailingZeros(('0.' + '000000000000000000'.substr(0, 18 - str.length) + str).substr(0, 18 - str.length + precision + 2))
  else return [str.substr(0, str.length - 18), str.slice(-18)].join('.').substr(0, str.length - 18 + precision + 1)
}


export default {
  name: "Bridge",
  components: {
    EthBridge,
    BscBridge,
  },
  data() {
    return {
      showEthBridge: true,
      showBscBridge: false,
      balanceNumStr_Ether1: '',
      directionIndicatorEthToEtho: false,
      directionIndicatorEthoToEth: true,
      provider: providers.Web3Provider | null,
      signer: providers.JsonRpcSigner | null,
      wallet: '',
      currentLocked: '',
      currentApproved: '',
      currentBalance: '',
      balanceNumStr_TKN: '',
      inputAmount: '',
      costsInfo: {
        WETHOtoETHO: '',
        ETHOtoWETHO: ''
      } | null,
      paramsInfo: {
        costToFeeQuotient: 0,
        feeToMinQuotient: 0,
        preShutdownFlag: false
      } | null,
      warningMessage: '',
      hintMessage: '',
      successMessage: '',
      loading_request: false,
      loading_controllerInfo: true,
      loading_contractInfo: true,
      inBetween: false,
      hashes: { txHashCollect: '', txHashDispense: '' } | null,
    }
  },
  computed: {
    approvedEnough() {
      if (!this.controllerInfoOk || !this.contractInfoOk) return false
      return this.approvedBN.gte(this.minBNStr)
    },
    approvedBN() {
      return BigNumber.from(this.currentApproved)
    },
    amountEqual() {
      if(this.directionIndicatorEthoToEth) {
        return this.amountBN.eq(this.currentLocked)
      }
      return this.amountBN.eq(this.currentApproved)
    },
    entryExists() {
      if (!this.contractInfoOk || !this.currentLocked) return false
      return BigNumber.from(this.currentLocked).gt(0)
    },
    canMaximizeAmount() {
      return !this.approvedNonZero && this.amountBN.lt(this.currentBalance)
    },
    amountReceivedBNStr_ready() {
      if (!this.controllerInfoOk || !this.contractInfoOk) return 'NaN'
      return BigNumber.from(this.currentLocked).sub(this.feeBNStr).toString()
    },
    providerOk() {
      return !!this.provider && !!this.signer && !!this.wallet
    },
    controllerInfoOk() {
      return !!this.costsInfo && !!this.paramsInfo
    },
    contractInfoOk() {
      if(this.directionIndicatorEthoToEth) {
        return !!this.currentLocked && !!this.currentBalance
      }
      return !!this.currentApproved && !!this.currentBalance
    },
    allSafe() {
      return this.providerOk && this.controllerInfoOk && this.contractInfoOk && !this.warningMessage && !this.loading_controllerInfo
    },
    requestDisabled() {
      return !this.allSafe || !this.balanceEnough || (!this.amountEnough && !this.aboutToNullify)
    },
    aboutToNullify() {
      return this.amountZero && this.entryExists
    },
    amountZero() {
      return this.inputAmount === '0'
    },
    amountValid() {
      return !!Number(this.inputAmount) || this.amountZero
    },
    amountEnough() {
      if (!this.controllerInfoOk) return false
      return this.amountBN.gte(this.minBNStr)
    },
    balanceEnough() {
      if (!this.controllerInfoOk) return false
      return this.amountBN.lte(this.currentBalance)
    },
    lockedEnough() {
      if (!this.controllerInfoOk || !this.contractInfoOk) return false
      return BigNumber.from(this.currentLocked).gte(this.minBNStr)
    },
    amountBN() {
      if (!this.amountValid) return BigNumber.from(0)
      return numstrToBN(this.inputAmount)
    },
    amountReceivedBNStr() {
      if (!this.controllerInfoOk) return 'NaN'
      return this.amountBN.sub(this.feeBNStr).toString()
    },
    feeBNStr() {
      if (!this.controllerInfoOk) return 'NaN'
      if(this.directionIndicatorEthoToEth) {
        return BigNumber.from(this.costsInfo.ETHOtoWETHO).mul(this.paramsInfo.costToFeeQuotient).div('100').toString()
      } else {
        return BigNumber.from(this.costsInfo.WETHOtoETHO).mul(this.paramsInfo.costToFeeQuotient).div('100').toString()
      }
      
    },
    minBNStr() {
      if (!this.controllerInfoOk) return 'NaN'
      return BigNumber.from(this.costsInfo.ETHOtoWETHO)
        .mul(this.paramsInfo.costToFeeQuotient)
        .mul(this.paramsInfo.feeToMinQuotient)
        .mul('120')
        .div('1000000')
        .toString()
    },
  },
  async mounted() {
    await this.loadControllerInfo()
    if (this.controllerInfoOk) await this.connectProvider()
    this.loading_contractInfo = false
    setInterval(async () => {
      if (this.controllerInfoOk) {
        this.loading_controllerInfo = true
        await this.loadControllerInfo()
      }
    }, 30000)
    window.ethereum.on('networkChanged', () => {
      this.updateNetwork();
    })
  },
  methods: {
    async changeEthDirection() {
      this.warningMessage = '';
      this.hintMessage = '';
      this.successMessage = '';
      if(this.directionIndicatorEthToEtho) {
        this.directionIndicatorEthToEtho = false;
        this.directionIndicatorEthoToEth = true;
        this.loading_controllerInfo = true
        await this.loadControllerInfo()
        if (this.controllerInfoOk) await this.connectProvider()    
          this.loading_contractInfo = false
      } else {
        this.directionIndicatorEthToEtho = true;
        this.directionIndicatorEthoToEth = false;
        this.loading_controllerInfo = true
        await this.loadControllerInfo()
        if (this.controllerInfoOk) await this.connectProvider()
          this.loading_contractInfo = false

      }
    },
    async updateNetwork() {
      this.warningMessage = '';
      this.hintMessage = '';
      this.successMessage = '';
      this.loading_controllerInfo = true
      await this.loadControllerInfo()
      if (this.controllerInfoOk) await this.connectProvider()    
        this.loading_contractInfo = false
    },
    BNStrToNumstr,
    restoreInputAmount() {
      this.inputAmount = removeTrailingZeros(BNStrToNumstr(this.currentLocked, 18))
    },
    handleClickAppend() {
      if (this.approvedEnough && !this.amountEqual) this.restoreInputAmount()
      else if (this.canMaximizeAmount) this.maximizeInputAmount()
    },
    async loadEther1Balance() {
      if(this.directionIndicatorEthoToEth) {
        try {
          this.balanceNumStr_Ether1 = BNStrToNumstr((await providerO.getBalance(this.wallet)).toString())
        } catch (error) {
          console.error(error)
        }
      } else if(this.directionIndicatorEthToEtho) {
        try {
          this.balanceNumStr_Ether1 = BNStrToNumstr((await providerO.getBalance(this.wallet)).toString())
        } catch (error) {
          console.error(error)
        }
      }
    },
    async loadTKNBalance() {
      try {
        this.balanceNumStr_TKN = BNStrToNumstr((await TKN.balanceOf(this.wallet)).toString())
      } catch (error) {
        console.error(error)
      }
    },
    async connectProvider() {
      try {
        if (!window.ethereum) throw new Error('Please set up MetaMask properly')
        await (window.ethereum).enable()
        this.provider = new providers.Web3Provider((window.ethereum) || window.web3)

        if(this.directionIndicatorEthoToEth) {
          if ((await this.provider.getNetwork()).chainId !== 1313114)
            throw new Error('You selected wrong network in MetaMask. Make sure you imported and selected Etho Protocol network and refresh the page')
        } else if (this.directionIndicatorEthToEtho) {
          if ((await this.provider.getNetwork()).chainId !== 1)
          throw new Error('You selected wrong network in MetaMask. Make sure you selected Ethereum Mainnet and refresh the page')
        }
        this.signer = this.provider.getSigner()
        this.wallet = await this.signer.getAddress()
        await this.loadContractInfo()
        if (this.entryExists) this.restoreInputAmount()
      } catch (error) {
        this.warningMessage = 'Could not connect MetaMask. Error: ' + error.message
        console.error(error)
      }
    },
    async handleClickRequest() {
      this.loading_request = true
      if(this.directionIndicatorEthoToEth) {
        try {
          if (this.aboutToNullify) {
            await this.writeEntry()
          } else {
            if ((this.entryExists && !this.amountEqual) || !this.entryExists) await this.writeEntry()
            await this.requestOE()
          }
          this.inputAmount = ''
        } catch (error) {
          this.hintMessage = error.message
          console.error(error)
        }
      } else if(this.directionIndicatorEthToEtho) {
        try {
          if ((this.approvedNonZero && !this.amountEqual) || !this.approvedNonZero) await this.approve()
            await this.requestOE()
            this.inputAmount = ''
        } catch (error) {
          this.hintMessage = error.message
          console.error(error)
        }
      }

      this.inBetween = false
      this.loading_request = false
    },
    async approve() {
      const TKN = new Contract(address_TKN, Token.abi, this.signer)
      const ptx = await TKN.populateTransaction.approve(address_BA, this.amountBN)
      console.log({
        ptx,
        amt: this.amountBN.toString(),
        bal: this.currentBalance,
      })
      const tx = await this.signer.sendTransaction(ptx)
      console.log({ tx })
      const receipt = await tx.wait()
      console.log({ receipt })
      await this.loadContractInfo()
      this.inBetween = true
    },
    async writeEntry() {
      const BAO = new Contract(address_BAO, BridgeAssistO.abi, this.signer)
      const ptx = await BAO.populateTransaction.writeEntry()
      ptx.value = this.amountBN
      console.log({
        ptx,
        amt: this.amountBN.toString(),
        bal: this.currentBalance,
      })
      const tx = await this.signer.sendTransaction(ptx)
      console.log({ tx })
      const receipt = await tx.wait()
      console.log({ receipt })
      await this.loadContractInfo()
      this.inBetween = true
    },
    async requestOE() {
      if(this.directionIndicatorEthoToEth) {
        try {
          this.hashes = (await Axios.get(`https://ethobridge.uc.r.appspot.com/ETHOtoWETHO/${this.wallet}`)).data
          console.log(this.hashes)
        } catch (error) {
          console.error({ ...error })
          throw new Error(error.response.data)
        }
        await this.loadContractInfo()
        this.successMessage = 'Success! Save transaction hashes below'
      } else if (this.directionIndicatorEthToEtho) {
        try {
          this.hashes = (await Axios.get(`https://ethobridge.uc.r.appspot.com/WETHOtoETHO/${this.wallet}`)).data
          console.log(this.hashes)
        } catch (error) {
          console.error({ ...error })
          throw new Error(error.response.data)
        }
        await this.loadContractInfo()
        this.successMessage = 'Success! Save transaction hashes below'
      }
    },
    async loadControllerInfo() {
      try {
        this.paramsInfo = ((await db.collection('config').doc('changeables').get()).data()) || null
        if (!this.paramsInfo) throw new Error('Received invalid data from firestore')
        try {
          this.costsInfo = (await Axios.get('https://ethobridge.uc.r.appspot.com/info/costs')).data
        } catch (error) {
          throw error.response?.data || error.message
        }
        if (this.paramsInfo.preShutdownFlag) this.warningMessage = 'Controller will soon shut down for maintenance. Usage is blocked. Please wait'
      } catch (error) {
        this.warningMessage = 'Could not load info from controller. Usage is blocked. Try refreshing the page, try later or contact support'
        console.error(error)
      }
      this.loading_controllerInfo = false
    },
    async loadContractInfo() {
      if(this.directionIndicatorEthoToEth) {
        try {
          const BAO = new Contract(address_BAO, BridgeAssistO.abi, this.signer)
          var lockedAmount = await BAO.entryOf(this.wallet);
          var thisBalance = await this.signer.getBalance();
          //const res = await Promise.all([BAO.entryOf(this.wallet), this.signer.getBalance()])
          this.currentLocked = lockedAmount.toString()
          this.currentBalance = thisBalance.toString()
          if (thisBalance.lt(this.minBNStr) && !this.entryExists) this.warningMessage = 'Your balance is lower than minimum amount. Usage is blocked'
          else if (this.entryExists && !this.lockedEnough) {
            this.hintMessage = 'The locked amount is lower than minimum amount. You need to erase entry and write it again'
          }
          await this.loadTKNBalance()
        } catch (error) {
          console.error(error)
          this.warningMessage = 'Could not load info from contract. Usage is blocked. Try refreshing the page, try later or contact support'
        }
      }
      else if(this.directionIndicatorEthToEtho) {
        try {
          const TKN = new Contract(address_TKN, Token.abi, this.signer)
          console.log("This Wallet: " + this.wallet);
          var approvedAmount = await TKN.allowance(this.wallet, address_BA);
          var balanceAmount = await TKN.balanceOf(this.wallet);
          //const res = await Promise.all([TKN.allowance(this.wallet, address_BA), TKN.balanceOf(this.wallet)])
          //console.log(res0);
          this.currentApproved = approvedAmount.toString()
          console.log(this.currentApproved);
          this.currentBalance = balanceAmount.toString()
          console.log(this.currentBalance);
          if (BigNumber.from(this.currentBalance).lt(this.minBNStr)) this.warningMessage = 'Your balance is lower than minimum amount. Usage is blocked'
            await this.loadEther1Balance()
        } catch (error) {
          this.warningMessage = 'Could not load info from contract. Usage is blocked. Try refreshing the page, try later or contact support'
        }
      }
    },
  },
};
</script>
