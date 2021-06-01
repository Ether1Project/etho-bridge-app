<template >
    <v-container fluid>
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
          <v-card  class="px-4 py-2" align="center" justify="center" height="80">
            <p class="text-left" style="font-size: 10px; margin:2px;">Balance:  <span class="font-weight-black">{{ BNStrToNumstr(currentBalance) }} ETHO </span></p> 
            <p class="text-left" style="font-size: 10px; margin:2px;">Fee:  <span class="font-weight-black">{{ BNStrToNumstr(feeBNStr) }} ETHO </span></p> 
            <p class="text-left" style="font-size: 10px; margin:2px;">Minimum:  <span class="font-weight-black">{{ BNStrToNumstr(minBNStr) }} ETHO </span></p> 
          </v-card>
        </v-col>

        <v-col cols="4" align="left">
        </v-col>

        <v-col cols="4">
          <v-card  class="px-4 py-8" align="center" justify="center" height="80">
            <p class="text-left" style="font-size: 10px; margin:2px;">Balance:  <span class="font-weight-black">{{ balanceNumStr_TKN }} ETHO </span></p> 
          </v-card>
        </v-col>
      </v-row>

      <v-row dense align="center" justify="center" v-if="directionIndicatorEthToEtho">
        <v-col cols="4" align="left">
          <v-card  class="px-4 py-8" align="center" justify="center" height="80">
            <p class="text-left" style="font-size: 10px; margin:2px;">Balance:  <span class="font-weight-black">{{ balanceNumStr_Ether1 }} ETHO </span></p>
          </v-card>
        </v-col>

        <v-col cols="4" align="left">
        </v-col>

        <v-col cols="4">
          <v-card  class="px-4 py-2" align="center" justify="center" height="80">
            <p class="text-left" style="font-size: 10px; margin:2px;">Balance:  <span class="font-weight-black">{{ BNStrToNumstr(currentBalance) }} ETHO </span></p> 
            <p class="text-left" style="font-size: 10px; margin:2px;">Fee:  <span class="font-weight-black">{{ BNStrToNumstr(feeBNStr) }} ETHO </span></p> 
            <p class="text-left" style="font-size: 10px; margin:2px;">Minimum:  <span class="font-weight-black">{{ BNStrToNumstr(minBNStr) }} ETHO </span></p> 
             
          </v-card>
        </v-col>
      </v-row>

      <v-row justify="center" align="center" v-if="contractInfoOk">
        <v-col cols="8">
          <v-text-field
            v-if="directionIndicatorEthoToEth"
            v-model="inputAmount"
            label="Amount"
            outlined
            dense
            required
            class="py-4"
            @click:append="restoreInputAmount"
                :rules="[
                  () => amountValid || 'Enter proper amount',
                  () => amountEnough || amountZero || 'Amount is too low',
                  () => balanceEnough || amountEqual || 'Balance too low',
                ]"
          ></v-text-field>
          <v-text-field
            v-if="directionIndicatorEthToEtho"
            v-model="inputAmount"
            label="Amount"
            outlined
            dense
            required
            @click:append="handleClickAppend"
            class="py-4"
            :rules="[() => amountValid || 'Enter proper amount', () => amountEnough || 'Amount is too low', () => balanceEnough || 'Balance too low']"
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
        <v-alert border="right" color="red lighten-2" dark v-if="!!warningMessage"><p class="text-center" style="color:white; font-size: 12px;">{{ warningMessage }}</p></v-alert>
        <v-alert border="right" color="yellow lighten-2" dark v-if="!!hintMessage"><p class="text-center" style="color:white; font-size: 12px;">{{ hintMessage }}</p></v-alert>
        <v-alert border="right" color="blue-grey" dark v-if="inBetween"><p class="text-center" style="color:white; font-size: 12px;">Swap initiated. Sending request to bridge controller. This may take several minutes to complete.</p></v-alert>
        <v-alert border="right" color="blue-grey" dark v-if="!!successMessage"><p class="text-center" style="color:white; font-size: 12px;">{{ successMessage }}</p></v-alert>

        <v-alert border="right" color="blue-grey" dark v-if="loading_controllerInfo"><p class="text-center" style="color:white; font-size: 12px;">Loading Contract Information</p></v-alert>
        <v-alert border="right" color="blue-grey" dark v-if="!!hashes"><p class="text-center" style="color:white; font-size: 12px;">Transaction hashes:
          <br />
          Collect (ETHO Network):
          <a style="color:black;" :href="`https://explorer.ether1.org/tx/${hashes.txHashCollect}`" target="_blank" rel="noopener noreferrer">
            {{ hashes.txHashCollect }}
          </a>
          <br />
          Dispense (BSC Network):
          <a style="color:black;" :href="`https://bscscan.com/tx/${hashes.txHashDispense}`" target="_blank" rel="noopener noreferrer">
            {{ hashes.txHashDispense }}
          </a></p>
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
    apiKey: 'AIzaSyB45_4vLMRKDdC4u1qluB-R225ZrOHSW8A',
    authDomain: 'ethobridge-bsc.firebaseapp.com',
    projectId: 'ethobridge-bsc',
    storageBucket: 'ethobridge-bsc.appspot.com',
    messagingSenderId: '237758082117',
    appId: '1:237758082117:web:2dc86d2b8a0a471e2fc72c',
  })
const db = firebase.firestore()
const address_TKN = '0x0E900AA6ed4244c43d597E6d2F8Fb3994303ED99'
const address_BA = '0x475c3902842330A51191AcC6bc10FdbC8AEFe136'
const address_BAO = '0x1f83d695924803c2461C664F5e49a88b679577f3'

const providerO = new providers.JsonRpcProvider('https://rpc.ether1.org')

const provider_MAINNET = new providers.JsonRpcProvider('https://bsc-dataseed.binance.org/')
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
  name: "BscBridge",
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
          if ((await this.provider.getNetwork()).chainId !== 56)
          throw new Error('You selected wrong network in MetaMask. Make sure you selected Binance Smart Chain network and refresh the page')
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
          this.hashes = (await Axios.get(`https://ethobridge-bsc.uc.r.appspot.com/ETHOtoWETHO/${this.wallet}`)).data
          console.log(this.hashes)
        } catch (error) {
          console.error({ ...error })
          throw new Error(error.response.data)
        }
        await this.loadContractInfo()
        this.successMessage = 'Success! Save transaction hashes below'
      } else if (this.directionIndicatorEthToEtho) {
        try {
          this.hashes = (await Axios.get(`https://ethobridge-bsc.uc.r.appspot.com/WETHOtoETHO/${this.wallet}`)).data
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
          this.costsInfo = (await Axios.get('https://ethobridge-bsc.uc.r.appspot.com/info/costs')).data
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
