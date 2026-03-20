# Crypst 🔐

> *Your Bitcoin. Your Legacy. On-chain.*

Crypst is the first Bitcoin inheritance protocol built on Stacks. Lock your sBTC, name your heir, and stay alive with a periodic heartbeat. Go silent — and your legacy streams automatically to your family. No lawyers. No banks. No custodians. Just code.

---

## The Problem

Over **4 million Bitcoin** are estimated to be permanently lost — most of it because the owner died and no one knew the keys. There is no on-chain solution for Bitcoin inheritance today. Traditional crypto wallets have no concept of "if I'm gone, send this to my family."

Crypst solves this with a trustless, self-sovereign protocol built entirely on Stacks using Clarity 4 smart contracts.

---

## How It Works

```
Owner locks sBTC → names a beneficiary → sets an inactivity window
         ↓
   Calls heartbeat() periodically to reset the timer
         ↓
   Goes inactive for the full window?
         ↓
   Beneficiary claims sBTC in monthly streaming tranches
```

There is no third party at any point. The contract holds the sBTC. The contract enforces the rules. Bitcoin's own security backs every transaction via the Stacks Nakamoto layer.

---

## Features

### Heartbeat mechanism
The owner calls `heartbeat` once every X days/months. This resets the inactivity timer. As long as they're alive and active, the sBTC stays theirs. The moment they go silent past the deadline, the inheritance becomes claimable.

### Streaming inheritance
Unlike a lump-sum transfer, Crypst pays out in configurable monthly tranches — like a proper trust fund. Set it to 5% per month and your heir receives steady income over 20 months instead of one overwhelming windfall.

### Legal hash linking
Every will can store an optional IPFS hash of the owner's actual legal will document. The on-chain contract and the off-chain legal document stay permanently linked. Code and law, in sync.

### Full owner control
The owner can update their beneficiary, update the legal document hash, or cancel and reclaim their sBTC at any time — as long as the will is still active.

---

## Smart Contract Functions

| Function | Caller | Description |
|---|---|---|
| `create-will` | Owner | Lock sBTC, set beneficiary, inactivity window, stream interval, legal hash |
| `heartbeat` | Owner | Reset inactivity timer — proves liveness |
| `stream-claim` | Beneficiary | Pull one tranche of sBTC after deadline passes |
| `cancel-will` | Owner | Reclaim sBTC and close the will |
| `update-beneficiary` | Owner | Change the designated heir |
| `update-legal-hash` | Owner | Update the linked IPFS legal document |

### Read-only

| Function | Returns |
|---|---|
| `get-will` | Full will details for an owner |
| `is-claimable` | Whether a will is past its deadline |
| `seconds-until-claimable` | Countdown in seconds |
| `tranches-remaining` | How many stream payouts are left |
| `seconds-until-next-tranche` | Time until next claim window |
| `get-legal-hash` | IPFS hash of linked legal document |

---

## Why Clarity 4

Crypst uses two features that are exclusive to Clarity 4 (activated on Stacks mainnet early 2026):

- **`block-time`** — returns the real Unix timestamp of the current block. We use this in 4 places: creating the deadline, heartbeat reset, lump-sum claim check, and streaming interval math. This is far more accurate than the old pattern of multiplying block height by an average block time.

- **`restrict-assets`** — enforces post-conditions on fungible token transfers inside the contract, ensuring the contract can never move more sBTC than explicitly declared in any given call.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Smart contract | Clarity 4 on Stacks |
| Asset | sBTC (SIP-010, 1:1 Bitcoin-backed) |
| Testing | Clarinet + Vitest |
| Frontend | React + TypeScript |
| Wallet | Hiro Wallet via `@stacks/connect` |
| Legal layer | IPFS (CIDv1, 32-byte hash) |

---

## Project Structure

```
crypst/
├── contracts/
│   └── crypst.clar          # Main Clarity 4 contract
├── tests/
│   └── crypst_test.ts       # Full Clarinet test suite
├── frontend/
│   ├── src/
│   │   ├── pages/
│   │   │   ├── Landing.tsx
│   │   │   ├── Dashboard.tsx    # Owner view
│   │   │   ├── Beneficiary.tsx  # Heir view
│   │   │   └── CreateWill.tsx   # Will setup form
│   │   ├── components/
│   │   └── lib/
│   │       └── contract.ts      # All contract call helpers
│   └── package.json
├── settings/
│   └── Devnet.toml
└── README.md
```

---

## Running Locally

### Prerequisites
- [Clarinet](https://github.com/hirosystems/clarinet) v2.0+
- Node 18+
- [Hiro Wallet](https://wallet.hiro.so) browser extension

### Contract

```bash
# Clone the repo
git clone https://github.com/yourname/crypst
cd crypst

# Check contract
clarinet check

# Run tests
clarinet test

# Open interactive console
clarinet console
```

### Frontend

```bash
cd frontend
npm install
npm start
```

Open `http://localhost:3000`, connect your Hiro wallet on testnet, and you're live.

---

## Testnet Deployment

Contract deployed on Stacks testnet:

```
Contract address: ST... (add after deployment)
Explorer: https://explorer.hiro.so/txid/...?chain=testnet
```

---

## Example: Creating a Will

```clarity
;; Lock 0.1 sBTC, stream 5% per month, 6-month inactivity window
(contract-call? .crypst create-will
  .sbtc-token                          ;; sBTC contract
  'ST2BENEFICIARY...                   ;; beneficiary address
  u1000000                             ;; amount in sats (0.01 sBTC)
  u15768000                            ;; inactivity window: 6 months in seconds
  u2592000                             ;; stream interval: 30 days in seconds
  u500                                 ;; tranche: 500 bps = 5% per interval
  (some 0x...)                         ;; IPFS hash of legal will (optional)
)
```

---

## Roadmap (Post-Hackathon)

- **Multi-sig heirs** — require 2-of-3 children to co-sign a claim
- **Social recovery** — 3 trusted friends can vote to reset your heartbeat key if lost
- **Mainnet deployment** with full audit
- **Mobile app** for heartbeat reminders

---

## Team

Built for **BUIDL Battle #2** on DoraHacks by:

- **[Your name]** — Frontend
- **Chandan** — Smart contracts

---

## License

MIT
