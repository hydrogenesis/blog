---
title: Notes on Cortex
date: 2022-09-21 14:35:05
tags:
---

During debugging, the following difference is found:

Ethereum
```bash
curl https://mainnet.infura.io/v3/c2fe81ba6e0e4ba2950f682bab7ee22c -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0", "method":"eth_call", "params":[{"to": "0xEa11755Ae41D889CeEc39A63E6FF75a02Bc1C00d", "data": "0x70a082310000000000000000000000003531addf2ce7877d54aa4ba0748ab261c3e5149a"}, "latest"], "id":1}'
```
returns:
```json
{"jsonrpc":"2.0","id":1,"result":"0x0000000000000000000000000000000000000000000000000000000000000000"}
```

Cortex
```bash
curl https://cortex.logistic.ml -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0", "method":"eth_call", "params":[{"to": "0x1cb2df6Fb7cA63Fc31A1d36C3EC1a5ec1C09c64D", "data": "0x70a082310000000000000000000000003531addf2ce7877d54aa4ba0748ab261c3e5149a"}, "latest"], "id":1}'
```
returns:
```json
{"jsonrpc":"2.0","id":1,"error":{"code":-32000,"message":"insufficient balance to pay for gas: address 0x0000000000000000000000000000000000000000 have 191425692757000000000 want 9223372036854775807000000000"}}
```

It turns out that if we add a "gas" param, it will be good:

```bash
curl https://cortex.logistic.ml -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0", "method":"eth_call", "params":[{"to": "0x1cb2df6Fb7cA63Fc31A1d36C3EC1a5ec1C09c64D", "data": "0x70a082310000000000000000000000003531addf2ce7877d54aa4ba0748ab261c3e5149a","gas":"0x2dc6c0"}, "latest"], "id":1}'
```
returns:
```json
{"jsonrpc":"2.0","id":1,"result":"0x00000000000000000000000000000000000000000000021e19e0c9bab2400000"}
```
# Solution Number One
(for uniswap v1-interface)
Add one line before node_modules/ethers/providers/base-provider.js:826

```js
if (params['transaction']['gasLimit'] == null) params['transaction']['gasLimit']='0x4c4b400';
```

# Solution Number Two
(for blockscout)
Change line 192 of file apps/ethereum_jsonrpc/lib/ethereum_jsonrpc/contract.ex to

```
%{to: contract_address, data: data, gas: "0x4c4b400"}
```
