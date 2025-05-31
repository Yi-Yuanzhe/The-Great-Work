+++
title = "My Arbitrage"
author = ["O'Stregone"]
description = "这是我使用 ox-hugo 从 Org Mode 发布的第一篇文章！"
date = 2025-05-31T00:00:00+08:00
draft = false
+++

## 基础建设与链上数据交互 {#基础建设与链上数据交互}


### 连接到以太坊 {#连接到以太坊}


#### Remote Procedure Call, RPC {#remote-procedure-call-rpc}

以太坊链由成千上万台计算机（节点）共同维护和更新。每个节点都保存着整个区块链的副本（或部分副本），并遵循相同的规则来验证交易和创建新区块。当需要与查询链上的数据，不可能访问所有的节点，因此需要一个“入口”，RPC 就充当这个入口。

首先，通过 `pip install web3` 安装 web3 这个 Python 库.

然后前往 www.infura.io 注册一个帐号并申请 RPC API，并通过下面的代码测试是否能够成功连接到 RPC 节点。

```python
from web3 import Web3

rpc_url = "https://mainnet.infura.io/v3/73a7f170007547dc982192de87b4465f"
web3 = Web3(Web3.HTTPProvider(rpc_url))

if web3.is_connected():
    print("成功连接到以太坊 RPC 节点！")
    print(f"当前区块号: {web3.eth.block_number}")
else:
    print("连接失败！")
```


### 理解 Uniswap V2 核心概念 {#理解-uniswap-v2-核心概念}


#### 交易对 (Pair) {#交易对--pair}

-   在 Uniswap V2 中，任何两种 ERC20 代币都可以组成一个交易对（Pair）。例如，WETH/USDC 是一个交易对，DAI/WETH 是另一个。
-   每个交易对本质上是一个独立的流动性池 (Liquidity Pool)。用户可以向这个池子提供两种代币（按特定比例），也可以从池子中用一种代币兑换另一种。
-   每个交易对在以太坊上都有一个独立的智能合约地址。


#### 流动性池 (Liquidity Pool) {#流动性池--liquidity-pool}

-   流动性池是 Uniswap V2 的核心。与传统的订单簿交易所不同，Uniswap 使用流动性池来促成交易。
-   池中锁定了两种代币的储备金。例如，WETH/USDC 池中会同时存有 WETH 和 USDC。
-   任何人都可以成为流动性提供者 (Liquidity Provider, LP)，通过向池中按当前价格比例存入两种代币来提供流动性，并获得代表其份额的 LP 代币。


#### 储备量 {#储备量}

储备量指在某个特定时刻，一个交易对合约中实际存储的两种代币的数量。例如，WETH/USDC 池可能有 1000 WETH 和 2,000,000 USDC 的储备。


#### 恒定乘积公式 {#恒定乘积公式}

x \* y = k
这是 Uniswap V2 定价的核心机制。当发生交易时（例如，用户用代币 A 换取代币 B），池中的 x 会增加，y 会减少。为了保持 x \* y = k 始终不变，代币的价格会根据储备量的变化而自动调整。

这意味着交易量越大 (相对于池子的储备量)，价格滑点越大。
