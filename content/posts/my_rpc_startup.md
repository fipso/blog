---
title: Trying to build my own Web3 RPC provider/hoster
description: Sometimes there is no way arround running your own BTC/ETH/SOL/XMR Node. I wish there was a shared hosting company that would just give one direct ssh or docker access to the Node's OS
date: 2025-09-28T12:00:00+02:00
draft: false
tags: [linux, server, hosting, crypto, web3, eth]
---

I know I am a little late to the Crypto/Web3 game now, but after the initial hype phase comes the actually building new (useful) tech phase. Lets forget about all the NFT projects that provide 5 pages of google slides as their 'white paper' and focus on the real new tech that Web3 (mostly ETH) introduced us to.

## The beginnigs
My initial experience with crypto currencies goes back to learning about Bitcoin in a YT video from Germany's most OG tech channel [Semper Video](https://www.youtube.com/@SemperVideo). After finding out that my parents didn't have a credit card and there was no way for 13 yo me to acquire said coins at 200$/BTC, I didnt bother much with crypto at all.  
  
Couple of years go by and one of my guys asks for support building a backend for a ETH based cryptokitties like game. They never actually shipped it, but I was learning a lot about ETH's internals while working on it.  
Let me quickly sum up the issues I encountered back then:
- ETH does not support tokens natively. All ERC20/ERC721 tokens are just decentralized Excel sheets that update balances upon contract interaction.
- Browser extension wallets suck:
    - One missclick and the window closes and resets all input
    - Features like gas fee prediction break all the time
    - Breaking API changes
- Reliably keeping your off-chain code in sync with the chain is tricky:
    - You can never rely on only WebSockets:
        - Every provider disconnects you once in a while
        - No guarantee that all events/txs are delivered via WS
    - Your app may need to be restarted
    - Some sort of resync logic is always needed
        - RPC providers limit max blocks per request

## DeFi is wild
At some point in time I finally stumbled upon the first Web3 technology that made me believe that crypto to is more then just boring digital gold. As you may have guessed it, I am talking about [Flash Loans](https://www.coinbase.com/en-de/learn/advanced-trading/what-is-a-flash-loan). TLDR: Flash Loans allow you to borrow millions worth of lets say ETH and perform a bunch of on-chain trades with it, for a very small fee. The only hook is: you have to give the borrowed money back in the same block that you requested it. Flash Loans are usually used for A: exploiting a bug in some poor DeFi projects contracts to steal millions or B: to make insane profits with Arbitrage trading. Arbitrage is about trading currencies between different centralized or decentralized exchanges and profiting from the price diff. A flash loan allows you to safely take profits without having millions in capital.  
  
Stuff gets even crazier once you realize **you can legally bribe ETH block producers to reorder transactions of other people inside a to be created new block**. This is called [MEV](#) but I wont go into the details here.

## The dark forest
For many sorts of applications its handy to have a look at unconfirmed/to be built blocks. This involves classic day to day activity like scamming darknet drug vendors by double spending btc /s or more serious stuff eg: trading bots. The unconfirmed transactions that live in a validators memory are usually referred to as mempool. Knowing what the mempool looks like is only possible by having access to a real non-validator node of the currency that you are working with. Its not possible to just ask every node via its low level p2p gossip protocol for the current mempools content, because nodes will drop clients that don't behave like a real node. Some nodes listen publicly for end user API requests via their JSON-RPC spec, but usually not allow mempool access. Even the RPC node providers that charge for API usage (usually a credit based system) not always allow mempool access.

## You probably just want to run your own Node
By reading all of this you probably now understand that running your own lets say ETH or BTC node is a really nice thing. Let me summarize why:
- Payed RPC API providers exist (Quicknode / Infura) but further move your infrastructure apart:
    - Node runs somewhere else meaning +100ms delay (JAM Stack era vibes)
    - Per request response size is limited to 500 blocks
    - Quicknode offers "backfilling" where you can run code near their DB, but the pricing is ridiculous
- Even running non-validator nodes yourself helps the P2P network grow
- Talk to the node via (Unix Domain) IPCs sockets (no TCP overhead)
    - Profit is questionable because because most client libs suck at IPC

## Introducing BLAZED.sh
So I think there is certainly a need for having a \<Insert Chain here\> RPC Node hosting service that allows you to run your own code directly on the same server that also runs the node. You have all the advantages of a node you got ssh access to + its not that expensive because the service is doing multi tenant shared hosting.
  
I spent a lot of time while doing my bachelors degree looking for untrusted code execution solutions and orchestrators to build such a PaaS (Platform as a Service). Right now I want to offer OCI (docker) container deployments onto synced up ETH nodes and also JS script execution. If such a service sounds useful to you, you should definitely checkout [BLAZED.sh](https://blazed.sh) 
