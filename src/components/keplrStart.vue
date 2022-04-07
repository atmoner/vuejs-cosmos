<template>
  <div class="hello">
  
    <p>Your address: {{ address }}</p>
    <br />
    <button v-on:click="sendTx">sendTx</button>
    <br />
    <p>{{ resultTx.transactionHash }}</p>
  </div>
</template>

<script>
import {
	assertIsDeliverTxSuccess,
	SigningStargateClient,
} from '@cosmjs/stargate'


export default {
  name: 'keplrStart',
  data: () => ({
    address: '',
    resultTx: ''
  }), 
  methods: {
  sendTx: async function() {
      // Partie envoie de tx 
      const chainId = 'osmosis-1';
      await window.keplr.enable(chainId);
      const offlineSigner = await window.getOfflineSignerAuto(chainId);
      const accounts = await offlineSigner.getAccounts();

      const client = await SigningStargateClient.connectWithSigner(
          'https://rpc-osmosis.blockapsis.com',
          offlineSigner
      )
      const convertAmount = 0.01 * 1000000
      const amount = {
          denom: 'uosmo',
          amount: convertAmount.toString(),
      }
      const fee = {
          amount: [{
              denom: 'uosmo',
              amount: '5000',
          }, ],
          gas: '200000',
      }

      try {
          const result = await client.sendTokens(accounts[0].address, this.address, [amount], fee, this.memo)
          assertIsDeliverTxSuccess(result)
          console.log(result)
          this.resultTx = result
      } catch (error) {
          console.error(error);
      }

    }
  }, 
  async mounted () {
    // Partie login, doit ajouter un button et retirer de mounted()
    window.onload = async () => {
        if (!window.getOfflineSigner || !window.keplr) {
            alert("Please install keplr extension");
        } else {
            if (window.keplr.experimentalSuggestChain) {
                try {
                    await window.keplr.experimentalSuggestChain({
                        chainId: "osmosis-1",
                        chainName: "Osmosis mainnet",
                        rpc: "https://rpc-osmosis.blockapsis.com",
                        rest: "https://lcd-osmosis.blockapsis.com",
                        stakeCurrency: {
                            coinDenom: "OSMO",
                            coinMinimalDenom: "uosmo",
                            coinDecimals: 6,
                        },
                        bip44: {
                            coinType: 118,
                        },
                        bech32Config: {
                            bech32PrefixAccAddr: "osmo",
                            bech32PrefixAccPub: "osmopub",
                            bech32PrefixValAddr: "osmovaloper",
                            bech32PrefixValPub: "osmovaloperpub",
                            bech32PrefixConsAddr: "osmovalcons",
                            bech32PrefixConsPub: "osmovalconspub"
                        },
                        currencies: [{
                            coinDenom: "OSMO",
                            coinMinimalDenom: "uosmo",
                            coinDecimals: 6,
                        }],
                        feeCurrencies: [{
                            coinDenom: "OSMO",
                            coinMinimalDenom: "uosmo",
                            coinDecimals: 6,
                        }],
                        coinType: 118,
                        gasPriceStep: {
                            low: 0.01,
                            average: 0.025,
                            high: 0.04
                        }
                    });
                } catch {
                    alert("Failed to suggest the chain");
                }
            } else {
                alert("Please use the recent version of keplr extension");
            }
        }

        const chainId = "osmosis-1";
        await window.keplr.enable(chainId);
        const offlineSigner = window.getOfflineSigner(chainId);
        const accounts = await offlineSigner.getAccounts();    
        console.log(accounts)
        this.address = accounts[0].address
    };    
  },  
}
</script>
