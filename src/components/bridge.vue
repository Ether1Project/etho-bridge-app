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
      <v-tab @click="showBscBridge=true, showEthBridge=false">BSC</v-tab>
    </v-tabs>

    <v-container v-if="showEthBridge" fluid>
      <v-row dense align="center" justify="center">
        <v-col>
          <v-card height="180" align="center" justify="center">
            <v-card-actions>
              <v-row dense  align="center" justify="center">
              <div class="d-none d-md-block justify-content-center ">
                <img :src='"../assets/images/etho/logo-only.svg"' style="height: 100px; object-fit: contain;" />
                <p class="custom-transform-class text-none">ETHO<br>
                <span class="custom-transform-class text-none" style="font-size: 10px;">Etho Protocol Network</span></p>
              </div>
            </v-row>
            </v-card-actions>
          </v-card>
        </v-col>

        <v-col align="center" justify="center">
          <v-card class="mx-16 py-0">
          <v-btn v-if="directionIndicatorEthoToEth" @click="changeEthDirection" icon>
            <v-icon>mdi-arrow-right-bold</v-icon>
          </v-btn>

          <v-btn v-if="directionIndicatorEthToEtho" @click="changeEthDirection" icon>
            <v-icon>mdi-arrow-left-bold</v-icon>
          </v-btn>
          </v-card>
          <p class="custom-transform-class text-none" style="font-size: 10px;">Swap Direction</p>
        </v-col>

        <v-col>
          <v-card height="180" align="center" justify="center">
            <v-card-actions>
              <v-row dense  align="center" justify="center">
              <div class="d-none d-md-block justify-content-center "  align="center" justify="center">
                <img :src='"../assets/images/etho/eth-logo.svg"' style="height: 100px; object-fit: contain;" />
                <p class="custom-transform-class text-none">Wrapped ETHO<br>
                <span class="custom-transform-class text-none" style="font-size: 10px;">Ethereum Network</span></p>
              </div>
            </v-row>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
      
      <v-row dense align="center" justify="center" v-if="directionIndicatorEthoToEth">
        <v-col cols="4" align="left">
          <v-card  class="px-4 py-2" align="center" justify="center" height="90">
            <p class="text-left" style="font-size: 12px; margin:2px;">Balance:  <span class="font-weight-black">{{ BNStrToNumstr(currentBalance) }} ETHO </span></p> 
            <p class="text-left" style="font-size: 12px; margin:2px;">Fee:  <span class="font-weight-black">{{ BNStrToNumstr(feeBNStr) }} ETHO </span></p> 
            <p class="text-left" style="font-size: 12px; margin:2px;">Minimum:  <span class="font-weight-black">{{ BNStrToNumstr(minBNStr) }} ETHO </span></p> 
          </v-card>
        </v-col>

        <v-col cols="4" align="left">
        </v-col>

        <v-col cols="4">
          <v-card  class="px-4 py-8" align="center" justify="center" height="90">
            <p class="text-left" style="font-size: 12px; margin:2px;">Balance:  <span class="font-weight-black">{{ balanceNumStr_TKN }} ETHO </span></p> 
          </v-card>
        </v-col>
      </v-row>

      <v-row dense align="center" justify="center" v-if="directionIndicatorEthToEtho">
        <v-col cols="4" align="left">
          <v-card  class="px-4 py-8" align="center" justify="center" height="90">
            <p class="text-left" style="font-size: 12px; margin:2px;">Balance:  <span class="font-weight-black">{{ balanceNumStr_TKN }} ETHO </span></p>
          </v-card>
        </v-col>

        <v-col cols="4" align="left">
        </v-col>

        <v-col cols="4">
          <v-card  class="px-4 py-2" align="center" justify="center" height="90">
            <p class="text-left" style="font-size: 12px; margin:2px;">Balance:  <span class="font-weight-black">{{ BNStrToNumstr(currentBalance) }} ETHO </span></p> 
            <p class="text-left" style="font-size: 12px; margin:2px;">Fee:  <span class="font-weight-black">{{ BNStrToNumstr(feeBNStr) }} ETHO </span></p> 
            <p class="text-left" style="font-size: 12px; margin:2px;">Minimum:  <span class="font-weight-black">{{ BNStrToNumstr(minBNStr) }} ETHO </span></p> 
             
          </v-card>
        </v-col>
      </v-row>

      <v-row justify="center" align="center" v-if="contractInfoOk">
        <v-col cols="8">
          <v-text-field
            v-model="inputAmount"
            label="Amount"
            @change="updateAmount"
            outlined
            dense
            required
            class="py-4"
            :append-icon="lockedEnough && !amountEqual ? 'mdi-restore' : ''"
                :rules="[
                  () => amountValid || 'Enter proper amount',
                  () => amountEnough || amountZero || 'Amount is too low',
                  () => balanceEnough || amountEqual || 'Balance too low',
                ]"
          ></v-text-field>
          <v-card class="mb-8" height="40" align="center" justify="center" v-if="directionIndicatorEthoToEth && amountEnough">
            <p class="text-center py-2" style="font-size: 12px;">You will receive:  <span class="font-weight-black">{{ amountEnough ? '~' + BNStrToNumstr(amountReceivedBNStr) : '...' }}  Wrapped ETHO </span><img :src='"../assets/images/etho/erc20.jpg"' style="height: 30px; object-fit: contain;" /></p> 
          </v-card>
          <v-card  class="mb-8" height="40" align="center" justify="center" v-if="directionIndicatorEthToEtho && amountEnough">
            <p class="text-center py-2" style="font-size: 12px;">You will receive:  <span class="font-weight-black">{{ amountEnough ? '~' + BNStrToNumstr(amountReceivedBNStr) : '...' }}  ETHO </span><img :src='"../assets/images/etho/logo.png"' style="margin-left:16px; height: 18px; object-fit: contain;" /></p> 
          </v-card>
        </v-col>
      </v-row>

      <v-card class="mt-10 mx-16" width="200">
          <v-btn text :color="aboutToNullify ? 'warning' : 'success'" large :loading="loading_request" :disabled="requestDisabled" @click="handleClickRequest">
            {{ aboutToNullify ? 'Take Back' : 'Request Swap' }}
          </v-btn>
      </v-card>

      <v-row justify="center" align="center" class="mt-8">
        <v-alert border="right" color="red lighten-2" dark v-if="!!warningMessage">{{ warningMessage }}</v-alert>
        <v-alert border="right" color="yellow lighten-2" dark v-if="!!hintMessage">{{ hintMessage }}</v-alert>
        <v-alert border="right" color="blue-grey" v-if="inBetween">Swap initiated. Sending request to bridge controller. This may take several minutes to complete.</v-alert>
        <v-alert border="right" color="blue-grey" v-if="!!successMessage">{{ successMessage }}</v-alert>

        <v-alert border="right" color="blue-grey" dark v-if="loading_controllerInfo">Loading Contract Information</v-alert>
        <v-alert border="right" color="blue-grey" dark v-if="!!hashes">Transaction hashes:
          <br />
          Collect (ETHO Network):
          <a :href="`https://explorer.ether1.org/tx/${hashes.txHashCollect}`" target="_blank" rel="noopener noreferrer">
            {{ hashes.txHashCollect }}
          </a>
          <br />
          Dispense (ETH Network):
          <a :href="`https://etherscan.io/tx/${hashes.txHashDispense}`" target="_blank" rel="noopener noreferrer">
            {{ hashes.txHashDispense }}
          </a>
        </v-alert>
      </v-row>

      <v-row dense>
        <v-col>
          <v-card>
            <v-card-actions>
              <v-spacer></v-spacer>

              <v-btn icon class="px-14" href="https://info.uniswap.org/#/tokens/0x99676c9fa4c77848aeb2383fcfbd7e980dc25027" target="_blank">
                <img :src='"../assets/images/etho/uniswap.png"' style="margin-left:16px; height: 18px; object-fit: contain;" />
              </v-btn>

              <v-btn icon class="px-14" href="https://etherscan.io/token/0x99676c9fa4c77848aeb2383fcfbd7e980dc25027" target="_blank">
                <img :src='"../assets/images/etho/etherscan-logo.png"' style="margin-left:16px; height: 18px; object-fit: contain;" />
              </v-btn>

   
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
    </v-container>

    <v-container v-if="showBscBridge" fluid>
      <v-row dense align="center" justify="center">
        <v-col>
          <v-card height="180" align="center" justify="center">
            <v-card-actions>
              <v-row dense  align="center" justify="center">
              <div class="d-none d-md-block justify-content-center ">
                <img :src='"../assets/images/etho/logo-only.svg"' style="height: 100px; object-fit: contain;" />
                <p class="custom-transform-class text-none">ETHO<br>
                <span class="custom-transform-class text-none" style="font-size: 10px;">Etho Protocol Network</span></p>
              </div>
            </v-row>
            </v-card-actions>
          </v-card>
        </v-col>

        <v-col align="center" justify="center">
          <v-card class="mx-16 py-0">
          <v-btn v-if="directionIndicatorEthoToEth" @click="changeEthDirection" icon>
            <v-icon>mdi-arrow-right-bold</v-icon>
          </v-btn>

          <v-btn v-if="directionIndicatorEthToEtho" @click="changeEthDirection" icon>
            <v-icon>mdi-arrow-left-bold</v-icon>
          </v-btn>
          </v-card>
          <p class="custom-transform-class text-none" style="font-size: 10px;">Swap Direction</p>
        </v-col>

        <v-col>
          <v-card height="180" align="center" justify="center">
            <v-card-actions>
              <v-row dense  align="center" justify="center">
              <div class="d-none d-md-block justify-content-center "  align="center" justify="center">
                <img :src='"../assets/images/etho/bsclogo.png"' style="height: 100px; object-fit: contain;" />
                <p class="custom-transform-class text-none">Wrapped ETHO<br>
                <span class="custom-transform-class text-none" style="font-size: 10px;">Binance Smart Chain</span></p>
              </div>
            </v-row>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>

      <v-row dense align="center" justify="center" v-if="directionIndicatorEthoToEth">
        <v-col cols="4" align="left">
          <v-card  class="px-4 py-2" align="center" justify="center" height="90">
            <p class="text-left" style="font-size: 12px; margin:2px;">Balance:  <span class="font-weight-black">{{ BNStrToNumstr(currentBalance) }} ETHO </span></p> 
            <p class="text-left" style="font-size: 12px; margin:2px;">Fee:  <span class="font-weight-black">{{ BNStrToNumstr(feeBNStr) }} ETHO </span></p> 
            <p class="text-left" style="font-size: 12px; margin:2px;">Minimum:  <span class="font-weight-black">{{ BNStrToNumstr(minBNStr) }} ETHO </span></p> 
          </v-card>
        </v-col>

        <v-col cols="4" align="left">
        </v-col>

        <v-col cols="4">
          <v-card  class="px-4 py-8" align="center" justify="center" height="90">
            <p class="text-left" style="font-size: 12px; margin:2px;">Balance:  <span class="font-weight-black">{{ balanceNumStr_TKN }} ETHO </span></p> 
          </v-card>
        </v-col>
      </v-row>

      <v-row dense align="center" justify="center" v-if="directionIndicatorEthToEtho">
        <v-col cols="4" align="left">
          <v-card  class="px-4 py-8" align="center" justify="center" height="90">
            <p class="text-left" style="font-size: 12px; margin:2px;">Balance:  <span class="font-weight-black">{{ balanceNumStr_TKN }} ETHO </span></p>
          </v-card>
        </v-col>

        <v-col cols="4" align="left">
        </v-col>

        <v-col cols="4">
          <v-card  class="px-4 py-2" align="center" justify="center" height="90">
            <p class="text-left" style="font-size: 12px; margin:2px;">Balance:  <span class="font-weight-black">{{ BNStrToNumstr(currentBalance) }} ETHO </span></p> 
            <p class="text-left" style="font-size: 12px; margin:2px;">Fee:  <span class="font-weight-black">{{ BNStrToNumstr(feeBNStr) }} ETHO </span></p> 
            <p class="text-left" style="font-size: 12px; margin:2px;">Minimum:  <span class="font-weight-black">{{ BNStrToNumstr(minBNStr) }} ETHO </span></p> 
             
          </v-card>
        </v-col>
      </v-row>

      <v-row justify="center" align="center" v-if="contractInfoOk">
        <v-col cols="8">
          <v-text-field
            v-model="inputAmount"
            label="Amount"
            @change="updateAmount"
            outlined
            dense
            required
            class="py-4"
            :append-icon="lockedEnough && !amountEqual ? 'mdi-restore' : ''"
                :rules="[
                  () => amountValid || 'Enter proper amount',
                  () => amountEnough || amountZero || 'Amount is too low',
                  () => balanceEnough || amountEqual || 'Balance too low',
                ]"
          ></v-text-field>
          <v-card class="mb-8" height="40" align="center" justify="center" v-if="directionIndicatorEthoToEth && amountEnough">
            <p class="text-center py-2" style="font-size: 12px;">You will receive:  <span class="font-weight-black">{{ amountEnough ? '~' + BNStrToNumstr(amountReceivedBNStr) : '...' }}  Wrapped ETHO </span><img :src='"../assets/images/etho/bsc.png"' style="margin-left:8px; height: 14px; object-fit: contain;" /></p> 
          </v-card>
          <v-card  class="mb-8" height="40" align="center" justify="center" v-if="directionIndicatorEthToEtho && amountEnough">
            <p class="text-center py-2" style="font-size: 12px;">You will receive:  <span class="font-weight-black">{{ amountEnough ? '~' + BNStrToNumstr(amountReceivedBNStr) : '...' }}  ETHO </span><img :src='"../assets/images/etho/logo.png"' style="margin-left:16px; height: 18px; object-fit: contain;" /></p> 
          </v-card>
        </v-col>
      </v-row>

      <v-card class="mt-10 mx-16" width="200">
          <v-btn text :color="aboutToNullify ? 'warning' : 'success'" large :loading="loading_request" :disabled="requestDisabled" @click="handleClickRequest">
            {{ aboutToNullify ? 'Take Back' : 'Request Swap' }}
          </v-btn>
      </v-card>

      <v-row justify="center" align="center" class="mt-8">
        <v-alert border="right" color="red lighten-2" dark v-if="!!warningMessage">{{ warningMessage }}</v-alert>
        <v-alert border="right" color="yellow lighten-2" dark v-if="!!hintMessage">{{ hintMessage }}</v-alert>
        <v-alert border="right" color="blue-grey" v-if="inBetween">Swap initiated. Sending request to bridge controller. This may take several minutes to complete.</v-alert>
        <v-alert border="right" color="blue-grey" v-if="!!successMessage">{{ successMessage }}</v-alert>

        <v-alert border="right" color="blue-grey" dark v-if="loading_controllerInfo">Loading Contract Information</v-alert>
        <v-alert border="right" color="blue-grey" dark v-if="!!hashes">Transaction hashes:
          <br />
          Collect (ETHO Network):
          <a :href="`https://explorer.ether1.org/tx/${hashes.txHashCollect}`" target="_blank" rel="noopener noreferrer">
            {{ hashes.txHashCollect }}
          </a>
          <br />
          Dispense (BSC Network):
          <a :href="`https://bscscan.com/tx/${hashes.txHashDispense}`" target="_blank" rel="noopener noreferrer">
            {{ hashes.txHashDispense }}
          </a>
        </v-alert>
      </v-row>


      <v-row dense>
        <v-col>
          <v-card>
            <v-card-actions>
              <v-spacer></v-spacer>

              <v-btn icon class="px-14" href="https://pancakeswap.info/token/0x0e900aa6ed4244c43d597e6d2f8fb3994303ed99" target="_blank">
                <img :src='"../assets/images/etho/pancakeswap.png"' style="margin-left:16px; height: 18px; object-fit: contain;" />
              </v-btn>

              <v-btn icon class="px-14" href="https://bscscan.com/token/0x0e900aa6ed4244c43d597e6d2f8fb3994303ed99" target="_blank">
                <img :src='"../assets/images/etho/bscscan.svg"' style="margin-left:16px; height: 18px; object-fit: contain;" />
              </v-btn>

   
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
    </v-container>


  </v-card>
  </div>
  </div>
  </div>
</template>
<script>
/* eslint-disable camelcase */
//import Vue from 'vue'
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

const provider_MAINNET = new providers.InfuraProvider(1, 'bd9a66af45444f52844ef4f2b294730b')
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
  data() {
    return {
      showEthBridge: true,
      showBscBridge: false,
      bridgedEthoAmount: 1.00001,
      bridgedWethoAmount: 1.00001,
      ethoBalance: 1.00001,
      ethoFee: 1.00001,
      ethoMinimum: 1.00001,
      directionIndicatorEthToEtho: false,
      directionIndicatorEthoToEth: true,
      provider: providers.Web3Provider | null,
      signer: providers.JsonRpcSigner | null,
      wallet: '',
      currentLocked: '',
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
    amountEqual() {
      return this.amountBN.eq(this.currentLocked)
    },
    entryExists() {
      if (!this.contractInfoOk || !this.currentLocked) return false
      return BigNumber.from(this.currentLocked).gt(0)
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
      return !!this.currentLocked && !!this.currentBalance
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
      return BigNumber.from(this.costsInfo.ETHOtoWETHO).mul(this.paramsInfo.costToFeeQuotient).div('100').toString()
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
  },
  methods: {
    changeEthDirection() {
      if(this.directionIndicatorEthToEtho) {
        this.directionIndicatorEthToEtho = false;
        this.directionIndicatorEthoToEth = true;
      } else {
        this.directionIndicatorEthToEtho = true;
        this.directionIndicatorEthoToEth = false;
      }
    },
    BNStrToNumstr,
    restoreInputAmount() {
      this.inputAmount = removeTrailingZeros(BNStrToNumstr(this.currentLocked, 18))
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
        if ((await this.provider.getNetwork()).chainId !== 1313114)
          throw new Error('You selected wrong network in MetaMask. Make sure you imported and selected Ether-1 network and refresh the page')
        this.signer = this.provider.getSigner()
        this.wallet = await this.signer.getAddress()
        this.$accessor.setWallet(this.wallet)
        await this.loadContractInfo()
        if (this.entryExists) this.restoreInputAmount()
      } catch (error) {
        this.warningMessage = 'Could not connect MetaMask. Error: ' + error.message
        console.error(error)
      }
    },
    async handleClickRequest() {
      this.loading_request = true
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
      this.inBetween = false
      this.loading_request = false
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
      try {
        this.hashes = (await Axios.get(`https://ethobridge.uc.r.appspot.com/ETHOtoWETHO/${this.wallet}`)).data
        console.log(this.hashes)
      } catch (error) {
        console.error({ ...error })
        throw new Error(error.response.data)
      }
      await this.loadContractInfo()
      this.successMessage = 'Success! Save transaction hashes below'
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
      try {
        const BAO = new Contract(address_BAO, BridgeAssistO.abi, this.signer)
        const res = await Promise.all([BAO.entryOf(this.wallet), this.signer.getBalance()])
        this.currentLocked = res[0].toString()
        this.currentBalance = res[1].toString()
        if (res[1].lt(this.minBNStr) && !this.entryExists) this.warningMessage = 'Your balance is lower than minimum amount. Usage is blocked'
        else if (this.entryExists && !this.lockedEnough) {
          this.hintMessage = 'The locked amount is lower than minimum amount. You need to erase entry and write it again'
        }
        await this.loadTKNBalance()
      } catch (error) {
        console.error(error)
        this.warningMessage = 'Could not load info from contract. Usage is blocked. Try refreshing the page, try later or contact support'
      }
    },
  },
};
</script>
