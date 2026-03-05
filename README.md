[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## Stellar On-Chain Voting (Serverless dApp)

A purely serverless voting dApp built for the **Stellar Testnet**. An admin defines a poll in a static config file. Each poll option has its own **dedicated Stellar public key**. Users connect their **Freighter** wallet and vote by sending an ultra-small on-chain payment (**0.0000001 XLM**) to the option’s address. Results are tallied in real-time by querying **Horizon** directly from the frontend (no backend, no database).

The UI is intentionally **retro-brutalist**: light gray background, white cards with thick black borders, uppercase monospace headings, and flat geometric accents.

---

## Project Description (How it works)

- **Serverless architecture**: Everything runs in the browser (Next.js + React). There is **no backend API**.
- **Vote casting**:
  - The app builds a Stellar payment transaction using `@stellar/stellar-sdk`.
  - Freighter prompts the user to sign the transaction via `@stellar/freighter-api`.
  - The signed transaction is submitted to Horizon Testnet.
- **Vote tallying**:
  - The app queries Horizon payment history for each option address.
  - A “vote” is counted when Horizon shows an **incoming native XLM payment** to an option address with the exact amount **`0.0000001`**.

**Core files:**

- `frontend/src/config/pollConfig.js`: poll question, options, and destination public keys
- `frontend/src/hooks/usePollResults.js`: Horizon polling + tallying hook
- `frontend/ConnectWallet.jsx`: Freighter install/connection UI
- `frontend/PollPage.jsx`: option selection + vote transaction build/sign/submit
- `frontend/Results.jsx`: live results bar chart

---

## Setup / Installation

### Prerequisites

- **Node.js** (recommended: latest LTS)
- **Freighter** browser extension installed
- A **Stellar Testnet** account funded via Friendbot (for the voter wallet)

### 1) Clone and install dependencies

```bash
git clone <YOUR_REPO_URL>
cd <YOUR_REPO_FOLDER>/frontend
npm install
```

### 2) Configure the poll (static config)

Edit:

- `frontend/src/config/pollConfig.js`

Each option has a hardcoded `publicKey` destination address.

### 3) Fund the option addresses (required)

Important: **Each option address must exist on Testnet** before it can receive payments. In Stellar, sending a payment to an unfunded account will fail unless you use a `createAccount` operation (this app uses `payment`).

Use **Friendbot** to fund each option destination address:

- Friendbot: [`https://friendbot.stellar.org/`](https://friendbot.stellar.org/)
- Paste each `publicKey` from `pollConfig.js` into Friendbot and fund it once.

---

## Usage

### 1) Prepare Freighter

- Install Freighter: [Freighter](https://www.freighter.app/)
- In Freighter, switch network to **Testnet**
- Fund your **voter wallet** with Friendbot (Testnet XLM)

### 2) Run the app

```bash
cd frontend
npm run dev
```

Open `http://localhost:3000`.

### 3) Vote

- Click **Connect Freighter**
- Select a poll option
- Click **Vote**
  - You will see clear UI phases like “Waiting for signature…” and “Submitting…”
- After submission, the **Results** panel will update automatically as Horizon polling refreshes

### Screenshot

> Screenshot placeholder (add a real screenshot for submission):

![App screenshot](./docs/screenshot.png)

---

## Tech Stack

- **Next.js (App Router)** + **React**
- **Stellar SDK**: `@stellar/stellar-sdk`
- **Freighter API**: `@stellar/freighter-api`
- **Horizon API (Testnet)** for reading payments and submitting transactions

---

## Notes / Limitations (Bootcamp Scope)

- Vote tallying currently counts **incoming native payments** matching the exact configured amount.
- The transaction includes a memo (`Bootcamp Vote`), but **memo validation is not enforced** in the tallying logic yet.
- This is a **Testnet demo** and not production-hardened (rate limits, spam resistance, and stronger validation would be required for mainnet).

---

## License

MIT — see the badge above.

