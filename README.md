# BitForge - Advanced Cross-Chain Asset Bridge for Bitcoin–Stacks Ecosystem

## Overview

**BitForge** is a validator-secured cross-chain bridge that enables seamless and secure asset transfers between the Bitcoin main network and the Stacks Layer 2 ecosystem. By combining Bitcoin’s unmatched security guarantees with Stacks’ programmable smart contracts, BitForge provides an institutional-grade interoperability framework for decentralized applications, DeFi protocols, and enterprise integrations.

The protocol leverages **multi-signature validator consensus**, **cryptographic proofs**, and **safety-first mechanisms** (pause/resume, emergency recovery) to ensure a trust-minimized bridging experience without compromising decentralization.

---

## System Overview

The BitForge bridge introduces a **two-way pegged asset transfer mechanism**:

1. **Deposit (BTC → Stacks):**

   * A user sends Bitcoin to the bridge.
   * Validators verify the transaction and initiate a corresponding deposit on Stacks.
   * Once enough confirmations and validator signatures are collected, wrapped assets are minted for the user on Stacks.

2. **Withdrawal (Stacks → BTC):**

   * A user burns/moves wrapped assets on Stacks through the `withdraw` function.
   * A record is generated, which can be processed by off-chain relayers to release native BTC to the specified Bitcoin address.

The protocol is designed with **fail-safe mechanisms** (emergency pause, validator revocation, recovery procedures) to mitigate risk during abnormal conditions.

---

## Contract Architecture

### Core Components

* **Trait Definition (`bridgeable-token-trait`)**
  Defines the minimal interface for bridge-compatible tokens (transfer, get-balance).

* **Error Constants**
  Unified error codes (`u1000+`) for strict contract-level validation.

* **Protocol Constants**

  * `MIN-DEPOSIT-AMOUNT` / `MAX-DEPOSIT-AMOUNT`
  * `REQUIRED-CONFIRMATIONS`
  * `CONTRACT-DEPLOYER` (owner / admin authority)

* **State Variables**

  * `bridge-paused`: global operational status flag.
  * `total-bridged-amount`: cumulative bridged assets.
  * `last-processed-height`: latest processed block height (for replay protection).

* **Data Storage Maps**

  * `deposits`: mapping of BTC tx-hash → deposit record.
  * `validators`: registry of trusted validator principals.
  * `validator-signatures`: mapping of validator approvals per tx-hash.
  * `bridge-balances`: ledger of user balances on Stacks side.

---

### Administrative Functions

* **`initialize-bridge`** – Activates the protocol.
* **`pause-bridge` / `resume-bridge`** – Emergency pause control.
* **`add-validator` / `remove-validator`** – Validator management.
* **`emergency-withdraw`** – Recovery of locked assets in extreme scenarios.

---

### Core Bridge Functions

* **`initiate-deposit`** – Records a verified BTC transaction into the bridge contract.
* **`confirm-deposit`** – Validators confirm deposits using signatures; triggers asset crediting.
* **`withdraw`** – Users withdraw wrapped assets back to BTC, generating a withdrawal event.

---

### Read-Only Query Functions

* **`get-deposit`** – Fetch full deposit record by BTC transaction hash.
* **`get-bridge-status`** – Check whether the bridge is active or paused.
* **`get-validator-status`** – Verify if a principal is a registered validator.
* **`get-bridge-balance`** – Retrieve user’s balance in bridged assets.

---

### Validation Helpers

* **`is-valid-principal`** – Ensures principal is valid and not the contract owner.
* **`is-valid-btc-address`** – Lightweight structural validation of Bitcoin addresses.
* **`is-valid-tx-hash`** – Ensures BTC transaction hash format validity.
* **`is-valid-signature`** – Validates cryptographic signature size and non-nullity.
* **`validate-deposit-amount`** – Enforces deposit size constraints.

---

## Data Flow

### Deposit Flow (BTC → STX)

1. User deposits BTC to the bridge address.
2. Validators observe and validate the BTC transaction.
3. A validator calls `initiate-deposit` with tx-hash, amount, recipient.
4. Multiple validators provide signatures via `confirm-deposit`.
5. Once validated, the recipient’s Stacks balance is credited.

### Withdrawal Flow (STX → BTC)

1. User calls `withdraw` with amount and BTC recipient address.
2. The contract deducts balance and emits a withdrawal event.
3. Off-chain relayers read the event and release BTC to the specified address.

---

## Security & Safety Features

* **Pause/Resume Mechanism:** Full stop of bridge operations during suspicious activity.
* **Validator Consensus:** Requires multi-signature confirmation for deposits.
* **Emergency Recovery:** Admin-only fund recovery in critical cases.
* **Strict Validation Layers:** Every function enforces protocol constants and signature checks.

---

## Deployment & Integration

* **Contract Deployer:** Holds initial administrative rights, including validator setup.
* **Validators:** Must be added via `add-validator` to participate in consensus.
* **Applications:** Can query balances (`get-bridge-balance`) or deposit states (`get-deposit`) to integrate with BitForge.

---

## License

MIT License © 2025 BitForge Contributors
