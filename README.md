<h1 align="center">
  <img width="600" src="img/logo.jpg" alt="Secured Finance logo" />
</h1>

<h3 align="center">A protocol for financial transactions backed by crypto-assets.</h3>

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

## Presentatins
- [HackFS YouTube Submission](https://youtu.be/cJ7RFAyNHbY)
- [HackFS Presentation](https://github.com/Secured-Finance/secured-finance-docs/blob/master/basics/HackFS-Presentation.pdf)

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

## Contributors

Senshi M. Onions [Twitter: @onion797jp](https://twitter.com/onion797jp)
[Bakhtiiar Muzakparov](https://github.com/muzakparov)
[Bach Adylbekov](https://github.com/bahadylbekov)

## License

This project is licensed under the MIT license, Copyright (c) 2020 Secured Finance. For more information see `LICENSE.md`.