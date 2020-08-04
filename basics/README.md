# Basics of Secured Finance

This directory has diagrams to explain how each player, components, smart contracts interact with each other. The interaction is designed using FSM (Finite State Machine); therefore, it heavily depends on the states of loans and contracts.

## Players

- Market Maker
  - Users using the Secured Finance web app, and show their borrow/lend interest rates, bid/offer FX rates)
- Market.sol
  - A smart contract to gather all market information. It works like a database.
- Collateral.sol
  - A smart contract that collets ETH as collateral and manages the control of the funds' ownership based on its state (`EMPTY`, `AVAILABLE`, `IN_USE`, `MARGINCALL`, `PARTIAL_LIQUIDATION`, `LIQUIDATION`). For FIL as collateral, we keep txHash and let the user's counterparty confirm the FIL balance manually.
- Loan.sol
  - It is a smart contract that stores all the loans to manage schedules for coupon payments, calculate PV (present value), and control its state (`REGISTERED`, `WORKING`, `DUE`, `CLOSED`, `TERMINATED`).
- Scheduler
  - It stays on the user's browser (or our back-up server in case nobody is using the web app) and uses web3js pub-sub to catch a new blockhead as a clock-tick. Each tick kicks batch operations such as market updates, re-evaluate PV of all financial products for a margin call.
- Filecoin Network
  - The Filecoin network to send/receive and verify the balance.
- Market Taker
  - Users who see the market and take a deal. In a loan deal, they can be borrowers or lenders. To make a lending deal to borrow money, they have to collateralize ETH (or FIL) to the Collateral smart contract.

## The Lifecycle of Loans

A loan takes the following steps, depending on the states of Collateral and Loan contracts. Detailed sequence diagrams are shown in the next section.

1. Registration and Market Making
   1. Market Makers(makers) register themselves with their ETH and FIL addresses.
   2. To quote as borrowers, makers should collateralize their ETH ore FIL to custody address. In the ETH case, it is the address of Collateral contract.
   3. Before input quotes to the Market smart contract, makers pick random FIL address from Collateral contracts and update the balance. This is a mechanism to keep all FIL custody account balances up to date.
2. Loan Execution
   1. Market Takers(takers) pick the best loan rate for lending/borrowing. If takers want to borrow funds, takers' collateral amount must be greater than 150% of the borrowing amount.
   2. When a maker receives an event message for lending FIL, they should transfer FIL and input txHash to the Loan Contract.
   3. When a taker receives an event message for the arrival of borrowing FIL, they should verify the amount from the Filecoin network and confirm to start the loan.
3. Coupon Payments
   1. Periodically, the Loan contract checks the payment schedule and update the loan's state.
   2. Prior to the scheduled payments, there are schedules for notices (default to 2 weeks). After the notice period, the loan state changes to `DUE`, and the coupon payments should be made until payment time.
   3. If takers failed to pay coupons, the loan state changes to `PARTIAL LIQUIDATION` to cover up coupon payments using collateral.
   4. Suppose the collateral currency is different from loan currency. In that case, one of the best makers is nominated to be the liquidity provider, and they will convert partial ETH collateral to FIL with a very attractive rate (default to 120%).
   5. In any case, after the coupon payment dates, the loan state will be back to `WORKING` and the collateral state to be `IN_USE`.
   6.
4. Redemption
   1. Redemption works similarly to the coupon payments. However, the final states of the Loan and Collateral are different. If a loan ends with no liquidation, the loan state will be `CLOSED`, and the collateral state ends with `AVAILABLE`. If a loan ends with liquidation, the loan state will be `TERMINATED`, and the collateral state will be `EMPTY`.
5. Margin Call
   1. Periodically, the Loan contract updates PV (present value) of all loans and checks the collateral coverage. If PV goes up and the coverage gets below 150%, the collateral state will change from `MARGINCALL`.
   2. If takers upsize collateral and the coverage gets over 150%, the state will be back to `IN_USE`.
   3. However, if takers don't respond, and the coverage gets below 125%, then the collateral state will be shifted further to `LIQUIDATION`. Then the automatic liquidation process will begin.
6. Liquidation
   1. When the Collateral contract emits a message for liquidation, the best maker is nominated to be the liquidity provider, who is entitled to convert currency (FIL to ETH) back to the loan currency with a very attractive rate (receive 120% coverage collateral).
   2. The rest of the collateral (about 5%, depends on the market's volatility) will be reserved to support market makers, liquidity providers, and developers of this project.
   3. After liquidations, the loan state will be `TERMINATED`, and the collateral state will be `EMPTY`.

## Sequence Diagram

Each step with the state changes are displayed followed by its sequence diagram.

### (1) Registration to Market Making

```txt
Loan registration done
Loan state: REGISTERD
Collateral state: EMPTY or AVAILABLE
```

![Registration to Market Making](./svg/Registration%20to%20Market%20Making.svg)

### (2) Loan Execution

```txt
Initial collateral needed and covered
Loan state: REGISTERD -> WORKING
Collateral state: AVAILABLE -> IN_USE
```

![Loan Execution](./svg/Loan%20Execution.svg)

### (3) Coupon Payments

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
Loan state: DUE
Collateral state: IN_USE -> PARTIAL_LIQUIDATION

Coupon unpaid -> paid
Loan state: DUE -> WORKING
Collateral state: PARTIAL_LIQUIDATION -> IN_USE
```

![Coupon Payments](./svg/Coupon%20Payments.svg)

### (4) Redemption

```txt
Redemption due
Loan state: WORKING -> DUE
Collateral state: IN_USE
```

```txt
Redemption due -> paid
Loan state: DUE -> CLOSED
Collateral state: AVAILABLE
```

```txt
Redemption due -> unpaid
Loan state: DUE
Collateral state: IN_USE -> LIQUIDATION

Redemption unpaid -> paid
Loan state: DUE -> TERMINATED
Collateral state: LIQUIDATION -> EMPTY
```

![Redemption](./svg/Redemption.svg)

### (5) Margin Call

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

### (6) Liquidation

```txt
Liquidation
Loan state: WORKING -> TERMINATED
Collateral state: LIQUIDATION -> EMPTY
```

![Liquidation](./svg/Liquidation.svg)
