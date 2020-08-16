<h1 align="center">
  <img width="600" src="img/logo.jpg" alt="Secured Finance logo" />
</h1>

<h3 align="center">A protocol for financial transactions backed by crypto-assets.</h3>

[![badge](https://img.shields.io/badge/submit%20for-HackFS-blue)](https://hack.ethglobal.co/showcase/secured-finance-recTkx6c1RDoLeaQm)

## Introduction

Secured Finance is an institutional-grade financial transaction platform with automatic collateral management and mark-to-market mechanisms.

We designed a protocol from accumulated knowledge of [40-years](https://en.wikipedia.org/wiki/Swap_(finance)#History) of the financial industry that governs [558 trilions of dollars of OTC (i.e. peer-to-peer) derivative transactions](https://stats.bis.org/statx/srs/table/d5.1) and made the interbank market system open to the public.

With this protocol, we built scalable, flexible, decentralized peer-to-peer markets for loans and FX, and then we will add swaps and options as we progress.

## Financial Transactions

You can do the following traditional financial transactions with various formats.
A list of formats and examples is shown below. We can also create innovative structured products by combining these plain-vanilla transactions.

- Loan (FIL loan backed by ETH) <---- HackFS primary focus
- Deposit (FIL deposit to earn interest)
- Swap (FIL/ETH cross-currency swap)
- Option (FIL/ETH European call option)

## Target User

- Miners who want to borrow/lend their FIL without worrying about credit risk
- Investors who have long-term view and aim for excess return from markets
- Hedgers who want to reduce their current exposure of their crypto-assets
- Arbitragers who want to profit by providing liquidity to collateralized assets

## Key Components

- Payment management smart contract
- Collateral management smart contract
- Ecosystem design of primary & secondary market on financial transactions
- Liquidity management to support margin call operations

## Technologies and Tools

- Filecoin for main payment currency
- Ethereum for collateral management smart contract written in Solidity
- Fleek and IPFS to develop web application
- React for front-end framework for UI
- DID with ION or Elements for Identity and Key management
- Libp2p, PubSub, Raft consensus for p2p oracle node
- Set of smart contracts for p2p oracle on Ethereum network

## Presentations

- [HackFS ETHGlobal Official Showcase](https://hack.ethglobal.co/showcase/secured-finance-recTkx6c1RDoLeaQm)
- [HackFS YouTube Submission](https://youtu.be/cJ7RFAyNHbY)
- [HackFS Presentation Slide](https://github.com/Secured-Finance/secured-finance-docs/blob/master/basics/HackFS-Presentation.pdf)
- [Secured Finance Demo App](https://securedfinance.on.fleek.co/)

## Core Architecture

Our webapp orchestrates state machines in Ethereum Smart Contracts.
([Documentation](https://github.com/Secured-Finance/secured-finance-docs/tree/master/basics))

- Market.sol
- Collateral.sol
- Loan.sol

## Repositories

- [Web Application](https://github.com/Secured-Finance/Secured-Finance-app)
- [Smart Contracts](https://github.com/Secured-Finance/smart-contracts)
- [P2P Oracle Network](https://github.com/Secured-Finance/p2p-oracle-node)
- [P2P Oracle Contracts](https://github.com/Secured-Finance/p2p-oracle-smart-contracts)

- [See all repos](https://github.com/Secured-Finance)

## Contributors

[Senshi M. Onions](https://twitter.com/onion797jp)

[Bakhtiiar Muzakparov](https://github.com/muzakparov)

[Bach Adylbekov](https://github.com/bahadylbekov)

## License

This project is licensed under the MIT license, Copyright (c) 2020 Secured Finance. For more information see `LICENSE.md`.
