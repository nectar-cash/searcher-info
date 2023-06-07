# Arbitrum Searchers - How to connect to Nectar

---

Nectar has created a private transaction mempool where we can auction off MEV extraction rights to the highest bidder. This is an early version and a work in progress.

## How to connect to the auction house

---

To connect to the auction house you will need an Auth token, currently this is a manual process, please email us at searchers@nectar.cash

_TODO: add technical details of how to connect here_

## What we will send you

---

For each transaction we will send you a JSON-RPC 2.0 request with the method name `nectar_auctionTransaction`. The `data` parameter will contain the transaction `hash` along with `txWithoutSignature` which contains all the usual paramters you'd find in an `eth_sendTransaction` call (without the signature). There is also an `options` parameter, currently this only contains the Unix timestamp that the auction will close.

<details>
  <summary>Example</summary>
  
```json
method: "nectar_auctionTransaction",
 data: {
   hash: "0x5ea6f0b95294445c004e4769926770825518d02e06304e4106ea6568a2d12f3b",
   txWithioutSignature: {
     to: "0x09E18590E8f76b6Cf471b3cd75fE1A1a9D2B2c2b",
     from: "0x900984F781c7D0108119EBFD90A2c4d9ff43B103",
     value: { _hex: "0x00", _isBigNumber: true },
     gasLimit: { _hex: "0x2464d6", _isBigNumber: true },
     maxPriorityFeePerGas: { _hex: "0x00", _isBigNumber: true },
     maxFeePerGas: { _hex: "0x080befc0", _isBigNumber: true },
     nonce: 40,
     type: 2,
     chainId: 42161,
     data: "0xa9059cbb00000000000000000000000036cff8a90e1cb657728e7df1166fb429789782b7000000000000000000000000000000000000000000000000000467c5ff858000",
     hash: "0x5ea6f0b95294445c004e4769926770825518d02e06304e4106ea6568a2d12f3b",
     gasPrice: null,
     accessList: [],
   },
   options: { closeTimestamp: 1628580000 },
 }
````

</details>

## What to send to us

---

A successful bid will contain both the MEV transaction and the bid amount. Send to us a JSON-RPC 2.0 request with the method name `nectar_bidTransaction`. There should be a `data` parameter which contains both the original transaction `hash` and the complete transaction `tx` you wish to submit (including signature). There should also be a `bid` parameter which is the ETH price in wei you are willing to pay to have this transaction executed.

<details>
  <summary>Example</summary>

```json
 method: "nectar_bidTransaction",
 data: {
   hash: "0x5ea6f0b95294445c004e4769926770825518d02e06304e4106ea6568a2d12f3b",
   tx: {
     to: "0x68b3465833fb72A70ecDF485E0e4C7bD8665Fc45",
     from: "0x0590844C5a4CCC14f928813cDb909E3d24EbDEa5",
     value: { _hex: "0xd529ae9e860000", _isBigNumber: true },
     gasLimit: { _hex: "0x49df9d", _isBigNumber: true },
     maxPriorityFeePerGas: { _hex: "0x00", _isBigNumber: true },
     maxFeePerGas: { _hex: "0x080befc0", _isBigNumber: true },
     nonce: 38,
     type: 2,
     chainId: 42161,
     data: "0x5ae401dc000000000000000000000000000000000000000000000000000000006457794600000000000000000000000000000000000000000000000000000000000000400000000000000000000000000000000000000000000000000000000000000001000000000000000000000000000000000000000000000000000000000000002000000000000000000000000000000000000000000000000000000000000000e404e45aaf00000000000000000000000082af49447d8a07e3bd95bd0d56f35241523fbab1000000000000000000000000dfcd9117ebcde0d658eedf0898419638f56b698000000000000000000000000000000000000000000000000000000000000027100000000000000000000000000590844c5a4ccc14f928813cdb909e3d24ebdea500000000000000000000000000000000000000000000000000d529ae9e86000000000000000000000000000000000000000000000026ccaa143fba73bd5a4576000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
     hash: "0xdec74ef0875b8e5772c2dd3e7890e22dba0f2cb986cbae81f0a50e90fd5873e6",
     gasPrice: null,
     accessList: [],
     r: 0,
     s: 0,
     v: 0,
   },
   bid: "123456789",
 }
```

</details>
