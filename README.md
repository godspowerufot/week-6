# Neon EVM: Gas Estimation, Balance Changes, and Transaction Flow

## What the Scripts Do

### Script 1 – `approve()`

This script uses a Solana wallet to submit an `approve(spender, amount)` transaction for a USDC token on Neon EVM.

- **Sender (Solana wallet)**: Signs the transaction
- **Spender (Ethereum address)**: Granted an allowance of 1,750,090,504 units (assumed USDC, 6 decimals)
- **Result**:
  - Allowance increased from `1749786100` to `1750090504`
  - Transaction successfully scheduled and finalized
  - Shows the **approve()** logic works with Solana keys via Neon

Neon EVM transaction hash: [0x83d139cab7f7cd84a5800965630248830d52cc9c20adc381b1766ec25fd5de0e](https://neon-devnet.blockscout.com/tx/0x83d139cab7f7cd84a5800965630248830d52cc9c20adc381b1766ec25fd5de0e)

### Script 2 – `transfer()`

This script performs a `transfer(to, amount)` transaction using the same Solana wallet.

- **Sender Balance Before**: 5,000,000 (5 USDC)
- **Receiver Balance Before**: 0
- **Transferred**: 1,000,000 units (1 USDC)
- **Result**:
  - Sender balance → 4,000,000 (4 USDC)
  - Receiver balance → 1,000,000 (1 USDC)
  - Transaction finalized successfully
  - Confirms that **ERC-20 transfer logic** can be executed from a Solana key

Neon EVM transaction hash: [0x3ffae1c28c421372790382d591d2f54b8c171c3a2d21618da3dd632b9461b923](https://neon-devnet.blockscout.com/tx/0x3ffae1c28c421372790382d591d2f54b8c171c3a2d21618da3dd632b9461b923)

---

# OBSERVATION BASE ON THE NEON EVM APPROVAL AND TRANSFER

## 1. Gas Estimation

- The code uses `proxyApi.estimateScheduledTransactionGas` to estimate gas for Neon EVM transactions.
- If the sender's SPL token account (ATA) or approval is missing, the script adds the required Solana instructions before sending the transaction.
- If approval is missing, gas estimation for the transfer fails with `"execution reverted"` because the contract is not yet approved to spend the tokens.

---

## 2. Balance Changes

- **Before Transfer:**
  - Sender’s USDC balance: `5,000,000n`
  - Receiver’s USDC balance: `0n`
- **After Transfer:**
  - Sender’s USDC balance: `4,000,000n`
  - Receiver’s USDC balance: `1,000,000n`
- This confirms that `1 USDC` (with 6 decimals) was transferred from sender to receiver.
  THE Gas estimation is realtively low and fast

---

## 3. Transaction Hash Resolution from Neon RPC

- After submitting the scheduled transaction to Solana, the code waits and then queries Neon RPC for the EVM transaction hash using `neon_getTransactionBySenderNonce`.
- The Neon EVM transaction hash is printed, confirming the transaction was processed on the Neon EVM side.

---

## 4. Scheduled Transaction Signature

- Each transaction submitted to Solana returns a Solana transaction signature (e.g., `66jjaitodEWkmAftDpktbHCZGSMMSwGFi9WYhLd89v3A2Jh52psi9q9cGjWDuaqCA7NPk2hNhm1MKzwa73ZFZMg7`).
- This signature can be used to track the transaction on the Solana blockchain.

---

## 5. Approval Flow

- If the sender’s ATA does not have the correct delegate or approval, the code adds an approval instruction to the transaction.
- This is necessary for the Neon EVM contract to transfer tokens on behalf of the user.

---

## 6. Error Handling

- If the sender’s ATA does not exist, the code creates it.
- If the sender’s USDC balance is zero, the script exits and prompts the user to add USDC.
