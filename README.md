# Nectar: Searcher cheat sheet

---

Nectar.cash is working on returning MEV back to users. To begin we are working on Arbitrum and currently have access to appox 60k transactions/day. We have created a private transaction mempool where we auction off MEV extraction rights to the highest bidder. This is an early version and a work in progress.

## How to connect to the auction house

---

For searchers authentication we use a variation of [basic authentication](https://en.wikipedia.org/wiki/Basic_access_authentication) flow. To connect to our auction house you will need an authorization token. If you do not have one already then please email [searchers@nectar.cash](mailto:searchers@nectar.cash)
​

Once you have the token you can open up a websocket connection to the auction house at `auction.nectar.cash:8000`

​
Make sure to add the `Authorization` header to your request:
​

```http
Authorization: Basic your_auhorization_token
```

## What we will send you

---

Once connected to the auction house you will receive a [JSON-RPC 2.0](https://www.jsonrpc.org/specification) notification with the method name `nectar_auctionTransaction` for each transaction that is being auctioned. The `params` parameter will contain the transaction `hash` which we use from now on to identify this auction, along with `txWithoutSignature` which contains all the usual paramters you'd find in an `eth_sendTransaction` call (without the signature). There is also an `options` parameter, currently this only contains the Unix timestamp that the auction will end.

<details>
  <summary>Example</summary>
  
```json
{
  "method": "nectar_auctionTransaction",
  "jsonrpc": "2.0",
  "params": {
    "hash": "0x5ea6f0b95294445c004e4769926770825518d02e06304e4106ea6568a2d12f3b",
    "txWithioutSignature": {
      "to": "0x09E18590E8f76b6Cf471b3cd75fE1A1a9D2B2c2b",
      "from": "0x900984F781c7D0108119EBFD90A2c4d9ff43B103",
      "value": { "_hex": "0x00", "_isBigNumber": true },
      "gasLimit": { "_hex": "0x2464d6", "_isBigNumber": true },
      "maxPriorityFeePerGas": { "_hex": "0x00", "_isBigNumber": true },
      "maxFeePerGas": { "_hex": "0x080befc0", "_isBigNumber": true },
      "nonce": 40,
      "type": 2,
      "chainId": 42161,
      "data": "0xa9059cbb00000000000000000000000036cff8a90e1cb657728e7df1166fb429789782b7000000000000000000000000000000000000000000000000000467c5ff858000",
      "hash": "0x5ea6f0b95294445c004e4769926770825518d02e06304e4106ea6568a2d12f3b",
      "gasPrice": null,
      "accessList": []
    },
    "options": { "closeTimestamp": 1628580000 }
  }
}

````

</details>

## What to send to us

---

A successful bid will contain both your MEV transaction and the bid amount. Send to us a JSON-RPC 2.0 request with the method name `nectar_bidTransaction`. There should be a `params` parameter which contains both the original transaction `hash` and the complete transaction `tx` you wish to submit (including signature). There should also be a `bid` parameter which is the ETH price in wei you are willing to pay to have this transaction executed.

<details>
  <summary>Example</summary>

```json
{
  "method": "nectar_bidTransaction",
  "jsonrpc": "2.0",
  "params": {
    "hash": "0x5ea6f0b95294445c004e4769926770825518d02e06304e4106ea6568a2d12f3b",
    "tx": {
      "to": "0x68b3465833fb72A70ecDF485E0e4C7bD8665Fc45",
      "from": "0x0590844C5a4CCC14f928813cDb909E3d24EbDEa5",
      "value": { "_hex": "0xd529ae9e860000", "_isBigNumber": true },
      "gasLimit": { "_hex": "0x49df9d", "_isBigNumber": true },
      "maxPriorityFeePerGas": { "_hex": "0x00", "_isBigNumber": true },
      "maxFeePerGas": { "_hex": "0x080befc0", "_isBigNumber": true },
      "nonce": 38,
      "type": 2,
      "chainId": 42161,
      "data": "0x5ae401dc000000000000000000000000000000000000000000000000000000006457794600000000000000000000000000000000000000000000000000000000000000400000000000000000000000000000000000000000000000000000000000000001000000000000000000000000000000000000000000000000000000000000002000000000000000000000000000000000000000000000000000000000000000e404e45aaf00000000000000000000000082af49447d8a07e3bd95bd0d56f35241523fbab1000000000000000000000000dfcd9117ebcde0d658eedf0898419638f56b698000000000000000000000000000000000000000000000000000000000000027100000000000000000000000000590844c5a4ccc14f928813cdb909e3d24ebdea500000000000000000000000000000000000000000000000000d529ae9e86000000000000000000000000000000000000000000000026ccaa143fba73bd5a4576000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
      "hash": "0xdec74ef0875b8e5772c2dd3e7890e22dba0f2cb986cbae81f0a50e90fd5873e6",
      "gasPrice": null,
      "accessList": [],
      "r": 0,
      "s": 0,
      "v": 0
    },
    "bid": "123456789"
  }
}
````

</details>

## What happens next

---

At the end of the auction we will forward the users transaction and the auction winning transaction to the sequencer in quick sucsession. We will then send out a notification with the method `nectar_auctionResult` which will contain the `hash` of the original transaction (for identification), the `winner` which is the hash of the winning transaction, along with the winning `bid` amount.

Periodically we will send instructions for making the payment to cover your winning bids, failure to make payments in a timely manner will mean revokation of your Auth key.

<details>
  <summary>Example</summary>

```json
{
  "method": "nectar_auctionResult",
  "jsonrpc": "2.0",
  "params": {
    "hash": "0x5ea6f0b95294445c004e4769926770825518d02e06304e4106ea6568a2d12f3b",
    "winner": "0x074c6af40a9173af6214a57405b9e2dbf8b63ddf05a6100a7b54f78ee42b727b",
    "bid": "123456789"
  }
}
```

</details>
