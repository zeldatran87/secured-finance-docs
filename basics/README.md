# 🌿 Secured Finance Protocol

[![badge](https://img.shields.io/badge/submit%20for-HackFS-blue)](https://hack.ethglobal.co/showcase/secured-finance-recTkx6c1RDoLeaQm) [![badge](https://img.shields.io/badge/submit%20for-ETHOnline-ffe4b4)](https://hack.ethglobal.co/showcase/secured-finance-recJiyE8KWrV5VyHi)

[Secured Finance](https://secured-finance.com/) is an institutional-grade peer-to-peer transaction platform with automatic collateral management and built-in mark-to-market mechanisms. We designed a protocol replicating [40-years](https://en.wikipedia.org/wiki/Swap_(finance)#History) of an industry-standard that manages [558 trillions of dollars of OTC (i.e., peer-to-peer) derivative transactions](https://stats.bis.org/statx/srs/table/d5.1) and made the interbank market system open to the public. We built a protocol for a scalable, interoperable, decentralized, peer-to-peer banking business for crypto assets.

This document shows an example of a loan transaction with diagrams to explain how each player, network, and smart contracts interact. The communication is designed using FSM (Finite State Machine); therefore, tracing state transitions helps understand how this system works for a whole lifecycle of a financial transaction.

**Disclaimer:** Secured Finance is not an intermediary service provider; it is a protocol developer we are not a custodian. Since this is a peer-to-peer platform, the ownership of funds and private keys always remain on users; therefore, financial activity and decision making such as funds transfer, sending confirmations are to be made solely by users.

## Players

- Market Maker
  - Users using the Secured Finance web app and propose their borrow/lend interest rates to market takers. To start market-making, users have to collateralize ETH (20% of the loan amount) to the Collateral smart contract.
- Market Taker
  - Users who see the market maker's proposal and take a deal. In a loan deal, they will be borrowers or lenders. To make a lending deal to borrow money, they have to collateralize ETH (150% of the loan amount) to the Collateral smart contract.
- [Market.sol](https://github.com/Secured-Finance/smart-contracts/blob/master/contracts/Market.sol)
  - Smart contracts to gather market information (MoneyMarket, FX). It works like an on-chain database.
- [Collateral.sol](https://github.com/Secured-Finance/smart-contracts/blob/master/contracts/Collateral.sol)
  - A smart contract that takes ETH as collateral and manages the funds' ownership based on its state (`EMPTY`, `AVAILABLE`, `IN_USE`, `MARGIN_CALL`, `LIQUIDATION_IN_PROGRESS`, `LIQUIDATION`). For FIL as collateral, we keep txHash and let the user's counterparty confirm the FIL balance manually.
- [Loan.sol](https://github.com/Secured-Finance/smart-contracts/blob/master/contracts/Loan.sol)
  - It is a smart contract that stores all the loans to manage schedules for coupon payments, calculate PV (present value), and control its state (`REGISTERED`, `WORKING`, `DUE`, `PAST_DUE`, `CLOSED`, `TERMINATED`).
- Filecoin Network
  - The Filecoin network to send/receive and verify the balance of FIL custody accounts.
- Scheduler
  - It uses web3js pub-sub to catch a new blockhead as a clock-tick. Each tick triggers operations such as market update, collateral & loan PV re-evaluation, and their state updates for a margin call and various notifications.

## The Lifecycle of Loans

A loan takes one of the following 6 steps, depending on the states of Collateral and Loan contracts. Detailed sequence diagrams are shown in the next section.

### ✍️ 1. Registration, Collateralization, and Market Making

   1. Users register their ETH and FIL addresses to receive funds. They also register an ID string such as DID to anchor a government-issued identity and public keys so that a counterparty can choose who they want to trade to comply with AML and other financial regulations.
   2. To begin, users should collateralize their ETH to the collateral contract address.

### 🤝 2. Loan Execution

   1. Market Takers pick the best loan rate for lending/borrowing. If takers want to borrow funds, their collateral amount must be greater than 150% of the borrowing amount.
   2. When a maker receives an event message that their FIL lending proposal was taken, they should transfer FIL and input txHash to the Loan Contract.
   3. When a taker receives an event message for the arrival of borrowing FIL, they should confirm the amount to start the loan.
   4. The loan state changes from `REGISTERED` to `WORKING`, and the collateral state changes from `AVAILABLE` to `IN_USE`.

### 💫 3. Coupon Payments

   1. Periodically, the Loan contract checks the payment schedule and update the loan's state.
   2. Before the scheduled payments, the Loan contract emits messages for payment notice (default to 2 weeks prior). After the notice, the loan state changes to `DUE`, and the coupon payments should be made until the payment time.
   3. If takers failed to pay coupons, the loan state changes to `PAST_DUE` and the collateral state changes to `LIQUIDATION_IN_PROGRESS` to cover up coupon payments from borrower' collateral.
   4. 120% of the coupon payment amount (in ETH) is transferred from the borrower's collateral to the lender's collateral.
   5. When the coupon payment is completed, the loan state will be back to `WORKING` and the collateral state to be `IN_USE`.

### ✅ 4. Redemption

   1. Redemption works similarly to coupon payments. If a loan ends with no liquidation, the loan state will be `CLOSED`, and the collateral state ends with `AVAILABLE`.

### 🆘 5. Margin Call

   1. Periodically, the Loan contract updates the PV (present value) of all loans and checks the collateral coverage. If a borrowed loan PV goes up and therefore the collateral coverage gets below 150%, the collateral state will change from `IN_USE` to `MARGINCALL`.
   2. If takers upsize collateral and the coverage gets over 150%, the state will be back to `IN_USE`.
   3. However, if takers don't respond, and the coverage gets below 125%, we consider this a credit event; therefore, the collateral state will be shifted further to `LIQUIDATION`. Then the automatic liquidation process will begin. This guarentees the counter-party risk is covered. It works like a credit support annex that comes with ISDA Master agreement in traditional finance. Users should acknowledge this automatic liquidation feature.

### 🔄 6. Liquidation

   1. Similar to coupon payments liquidation, 120% of the loan PV (in ETH) is transferred from the borrower's collateral to the lender's collateral.
   2. After liquidations, the loan state will be `TERMINATED`, borrowers' collateral is released, and the collateral state will be back to `AVAILABLE`.

## Sequence Diagram

Each step with the state changes is displayed, followed by its sequence diagram.
### (1) States for Registration, Collateralization, and Market Making ✍️

```txt
User and collateral registration
Collateral state: EMPTY -> AVAILABLE
```

![Registration to Market Making](./svg/Registration%20to%20Market%20Making.svg)

### (2) Loan Execution States 🤝

```txt
Initial collateral needed and covered
Loan state: REGISTERD -> WORKING
Collateral state: AVAILABLE -> IN_USE
```

![Loan Execution](./svg/Loan%20Execution.svg)

### (3) Coupon Payments States 💫

```txt
Coupon payment is due
Loan state: WORKING -> DUE
Collateral state: IN_USE
```

```txt
Coupon due -> paid
Loan state: DUE -> WORKING
Collateral state: IN_USE
```

```txt
Coupon due -> unpaid
Loan state: DUE -> PAST_DUE
Collateral state: IN_USE -> LIQUIDATION_IN_PROGRESS

Coupon unpaid -> paid
Loan state: PAST_DUE -> WORKING
Collateral state: LIQUIDATION_IN_PROGRESS -> IN_USE
```

![Coupon Payments](./svg/Coupon%20Payments.svg)

### (4) Redemption States ✅

```txt
Redemption due
Loan state: WORKING -> DUE
Collateral state: IN_USE
```

```txt
Redemption due -> paid
Loan state: DUE -> CLOSED
Collateral state: IN_USE -> AVAILABLE
```

```txt
Redemption due -> unpaid
Loan state: DUE -> PAST_DUE
Collateral state: IN_USE -> LIQUIDATION_IN_PROGRESS

Redemption unpaid
Loan state: PAST_DUE -> CLOSED
Collateral state: LIQUIDATION_IN_PROGRESS -> IN_USE -> AVAILABLE
```

![Redemption](./svg/Redemption.svg)

### (5) Margin Call States 🆘

```txt
Margin call (125% < coverage < 150%)
Loan state: WORKING
Collateral state: IN_USE -> MARGINCALL
```

```txt
Margin call covered
Loan state: WORKING
Collateral state: MARGINCALL -> IN_USE
```

```txt
Margin call failed (coverage < 125%)
Loan state: WORKING
Collateral state: MARGINCALL -> LIQUIDATION
```

![Margin Call](./svg/Margin%20Call.svg)

### (6) Liquidation States 🔄

```txt
Liquidation
Loan state: WORKING -> TERMINATED
Collateral state: LIQUIDATION -> LIQUIDATION_IN_PROGRESS -> IN_USE -> AVAILABLE
```

![Liquidation](./svg/Liquidation.svg)
