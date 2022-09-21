---
title: Use Uniswap SDK with Custom Network
date: 2022-09-17 21:22:55
tags:
---

to add custom network, go to

```
vim node_modules/@ethersproject/networks/lib/index.js
```

add the following code in the variable "networks" code block

```js
classic: {
    chainId: 61, 
    name: "classic",
    _defaultProvider: etcDefaultProvider("https:/\/www.ethercluster.com/etc", "classic")
},
```

note that you have to change the chainId and the url, as well as network name

for example:
```js
ethw: {
    chainId: 10001,
    name: "ethw",
    _defaultProvider: etcDefaultProvider("https:/\/mainnet.ethereumpow.org", "ethw")
},
```
