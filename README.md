

# STX-SatoshiSwaps - Decentralized BTC Options Trading Contract

This Clarity smart contract implements a **peer-to-peer, decentralized options trading platform** with **internal BTC balance tracking**. It allows users to create, purchase, and exercise **CALL and PUT options** on Bitcoin, fully on-chain without custodians.

---

## 🔧 Features

* 🔐 Internal BTC tracking via Clarity maps (`BTCBalances`)
* 📈 Options creation and trading for CALL and PUT types
* 💰 BTC deposits, withdrawals, and peer transfers
* ⏱ Option expiry and lifecycle management
* 📊 Oracle integration for external BTC price setting
* 👤 User option tracking (created and purchased)
* 🔒 Secure with multiple assertion checks for balance, authorization, and logic errors

---

## 🧱 Contract Architecture

### 📌 Constants

```clarity
CONTRACT-OWNER         ;; Owner of the contract
CALL / PUT             ;; Option types
Various ERR- codes     ;; Error constants (e.g., ERR-UNAUTHORIZED)
```

---

### 🗃 Data Maps

| Map           | Purpose                                                  |
| ------------- | -------------------------------------------------------- |
| `BTCBalances` | Tracks internal BTC balances per principal               |
| `Options`     | Stores all created options (CALL/PUT) with metadata      |
| `UserOptions` | Links users to created/purchased options (up to 20 each) |

---

### 🧮 Data Variables

* `next-option-id`: Auto-incrementing ID for new options
* `oracle-price`: Settable price of BTC by contract owner (used by exercising functions)

---

## 💸 BTC Management

### `deposit-btc(amount)`

Deposit `amount` of BTC into the contract’s internal ledger.

### `withdraw-btc(amount)`

Withdraw BTC from internal balance, asserting sufficient funds.

### `transfer-btc(from, to, amount)` *(private)*

Moves BTC internally between users, used in option exercise and purchases.

---

## 📄 Options Lifecycle

### Creating Options *(Coming Soon – assumed to be in another function)*

* Creator locks BTC as collateral
* Sets type (CALL/PUT), strike price, premium, expiry

### `exercise-call(...)` *(private)*

* Buyer pays strike price in STX to creator
* Receives BTC from contract

### `exercise-put(...)` *(private)*

* Creator pays strike price in STX to buyer
* Receives BTC from contract

---

## 👥 User Tracking

### `update-user-options(user, option-id, is-creator)` *(private)*

Appends the option ID to either `created` or `purchased` lists.

### Read-only Views

* `get-option(option-id)` — Retrieve full option data
* `get-user-options(user)` — Lists created/purchased options
* `get-btc-balance(holder)` — View BTC balance
* `get-btc-price` — View current oracle price

---

## 🔐 Authorization & Oracle

### `set-btc-price(price)`

Sets the BTC oracle price — **only callable by contract owner**.

---

## ❗ Errors

| Error Code                 | Description               |
| -------------------------- | ------------------------- |
| `ERR-EXPIRED`              | Option expired            |
| `ERR-INVALID-AMOUNT`       | Amount is invalid         |
| `ERR-UNAUTHORIZED`         | Unauthorized access       |
| `ERR-NOT-FOUND`            | Option or data not found  |
| `ERR-INSUFFICIENT-BALANCE` | Not enough BTC for action |
| `ERR-ALREADY-EXECUTED`     | Option already used       |
| `ERR-INVALID-PRICE`        | Strike price is invalid   |
| `ERR-OPTION-NOT-ACTIVE`    | Option is not active      |

---

