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

## Architecture

Our webapp orchestrates these smartcontracts using state machines. ([Sequence Diagram](https://github.com/Secured-Finance/secured-finance-docs/tree/master/basics))

- Market.sol
- Collateral.sol
- Loan.sol


## Contributors

Senshi M. Onions [Twitter: @onion797jp](https://twitter.com/onion797jp)
