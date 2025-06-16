# Neon EVM: Token Approval and Transfer via Solana Wallet

These scripts demonstrates how to interact with ERC-20 tokens on the **Neon EVM** using **Solana-native wallets**. It covers two fundamental operations:

1. **Granting a USDC token approval**
2. **Executing a USDC token transfer**

Both actions are initiated using a Solana keypair, showcasing how Neon maps Solana-based transactions into Ethereum-style interactions.

---

## 🛠 What the Scripts Do

### ✅ Script 1 – `approve()`

This script uses a Solana wallet to submit an `approve(spender, amount)` transaction for a USDC token on Neon EVM.

- **Sender (Solana wallet)**: Signs the transaction
- **Spender (Ethereum address)**: Granted an allowance of 1,750,090,504 units (assumed USDC, 6 decimals)
- **Result**:
  - Allowance increased from `1749786100` to `1750090504`
  - Transaction successfully scheduled and finalized
  - Shows the **approve()** logic works with Solana keys via Neon

Neon EVM transaction hash: [0x3e5c52a534f177627449d628ea1b53f435962d3f9f1919964497fbf52c59edb3](https://neon-devnet.blockscout.com/tx/0x3e5c52a534f177627449d628ea1b53f435962d3f9f1919964497fbf52c59edb3)

### ✅ Script 2 – `transfer()`

This script performs a `transfer(to, amount)` transaction using the same Solana wallet.

- **Sender Balance Before**: 5,000,000 (5 USDC)
- **Receiver Balance Before**: 0
- **Transferred**: 1,000,000 units (1 USDC)
- **Result**:
  - Sender balance → 4,000,000 (4 USDC)
  - Receiver balance → 1,000,000 (1 USDC)
  - Transaction finalized successfully
  - Confirms that **ERC-20 transfer logic** can be executed from a Solana key

Neon EVM transaction hash: [0x086aa16977fe280fc841a2ad878bc2dd2603f5244b9c86fc7ebdce636bf92b72](https://neon-devnet.blockscout.com/tx/0x086aa16977fe280fc841a2ad878bc2dd2603f5244b9c86fc7ebdce636bf92b72)
---

## 🔄 Understanding "Scheduled Transactions"

Neon doesn't submit Ethereum transactions directly. Instead:

- Transactions are **scheduled** by preparing a payload (e.g., `approve()` or `transfer()`)
- A Solana wallet signs and submits this payload using Neon’s `neon_estimateScheduledGas` and scheduling logic
- A **Solana transaction** is sent, and upon finalization, Neon executes the Ethereum-equivalent action
- Transaction hashes are available for both Solana and Neon EVM layers

This design allows Solana-native users to interact with Ethereum-like contracts without converting wallets.

---

## 🧠 Key Observations

- Neon bridges the **Solana execution environment** and **EVM behavior**, allowing Solana users to:
  - Grant ERC-20 token approvals
  - Transfer tokens
  - Pay gas via wrapped SOL or treasury accounts
- The `scheduledSolanaPayer` parameter links Solana-native gas payment to Neon EVM transactions
- Transaction finality happens only after both:
  1. The Solana transaction confirms
  2. The Neon EVM processes and publishes the EVM-compatible transaction
