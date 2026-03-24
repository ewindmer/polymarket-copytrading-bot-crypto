<div align="center">

[![Node](https://img.shields.io/badge/Node.js-18%2B-339933?style=flat-square&logo=node.js&logoColor=white)](https://nodejs.org)
[![MongoDB](https://img.shields.io/badge/MongoDB-Required-47A248?style=flat-square&logo=mongodb&logoColor=white)](https://mongodb.com)
[![Network](https://img.shields.io/badge/Polygon-USDC-8247E5?style=flat-square&logo=polygon&logoColor=white)](https://polygon.technology)
[![License](https://img.shields.io/badge/License-ISC-blue?style=flat-square)](LICENSE)

</div>

---

**3:47am.**

Car just moved on a market nobody was watching.

Position sized heavy. Conviction trade. The kind that looks obvious in hindsight and invisible in the moment.

You were asleep.

Your bot wasn't.

By 3:47:03 — two seconds after Car's fill hit the chain — your order was already in the queue. Same market. Proportional size. Same direction.

You woke up to a green position and a MongoDB log with 1 new entry.

**That's what this bot does.**

---

## The Trader

Car is one of Polymarket's most consistently profitable wallets. Not lucky — *consistent*. The kind of trader who shows up on leaderboards across political markets, sports, and macro events. High-conviction entries. Patient sizing.

The edge isn't secret. It's public, on-chain, observable.

This bot observes it — and acts on it before you've even checked your phone.

| | |
|---|---|
| **Profile** | [Car on Polymarket](https://polymarket.com/@Car?tab=activity) |
| **Wallet** | `0x7C3Db723F1D4d8cB9C550095203b686cB11E5C6B` |
| **Network** | Polygon · USDC |

---

## What Happens Every Second

While you're working, sleeping, or not watching charts — this is running:

```
[03:47:01] Polling Car's wallet...          no new activity
[03:47:02] Polling Car's wallet...          no new activity
[03:47:03] Polling Car's wallet...          TRADE detected
           Market    Will X happen by Y?
           Side      BUY
           Size      $4,200
           Price     0.61

[03:47:03] Validating event...              age OK (0s)
[03:47:03] Checking price drift...          current 0.62 — within threshold
[03:47:03] Scaling position...              your balance ÷ Car's balance × $4,200
[03:47:04] Submitting order...              OK — filled at 0.62
[03:47:04] Writing to MongoDB...            done

[03:47:04] Waiting for next signal...
```

That loop runs every second. Every hour. Every night you don't feel like watching markets.

---

## The Setup

You don't need to understand prediction markets.  
You don't need to track news cycles.  
You need three things: a funded Polygon wallet, MongoDB, and 5 minutes.

### 1. Clone

```bash
git clone https://github.com/LemnLabs/polymarket-trading-bot.git
cd polymarket-trading-bot
npm install
```

### 2. Configure

```bash
cp env.example .env
```

Open `.env`. Fill in exactly these:

```env
# The wallet you're following — already set to Car
USER_ADDRESS=0x7C3Db723F1D4d8cB9C550095203b686cB11E5C6B

# Your wallet
PROXY_WALLET=0xYourWalletAddress
PRIVATE_KEY=your_private_key_here        # keep this private

# Leave everything below as-is to start
CLOB_HTTP_URL=https://clob.polymarket.com
CLOB_WS_URL=wss://clob-ws.polymarket.com
RPC_URL=https://polygon-rpc.com
USDC_CONTRACT_ADDRESS=0x2791Bca1f2de4661ED88A30C99A7a9449Aa84174
MONGO_URI=mongodb://localhost:27017/polymarket_car
FETCH_INTERVAL=1
TOO_OLD_TIMESTAMP=24
RETRY_LIMIT=3
```

### 3. Run

```bash
npm run build && npm start
```

The bot is now live. It will log every poll, every detection, every fill.

---

## The Guardrails

The bot won't blindly chase every signal. Before placing any order it checks:

| Check | What it does |
|---|---|
| **Staleness filter** | Ignores trades older than 24 hours — no acting on old data |
| **Price drift gate** | Skips if the market has moved more than 5% from Car's fill |
| **Proportional sizing** | Scales your position to your balance — no overexposure |
| **Retry cap** | Attempts each order up to 3× before marking it as failed |
| **Full audit log** | Every detection and execution written to MongoDB |

---

## The Honest Part

Copy trading is not a guarantee.

Car takes losses. You will mirror some of them. Markets move between Car's fill and yours — sometimes in the wrong direction. RPC endpoints go down. APIs lag. Liquidity dries up.

**Start small. Watch the first 20 trades manually. Then decide if you scale.**

This software is for educational purposes. Not financial advice. Your capital, your responsibility.

---

## Under the Hood

```
src/
├── index.ts                  Entry point — env validation + orchestration
├── services/
│   ├── tradeMonitor.ts       Polls wallet activity, writes new trades to DB
│   └── tradeExecutor.ts      Reads pending trades, executes orders
├── utils/
│   ├── postOrder.ts          Buy / sell / merge order logic
│   ├── createClobClient.ts   CLOB API key derivation + client init
│   └── getMyBalance.ts       USDC balance read via Polygon RPC
├── models/
│   └── userHistory.ts        MongoDB schema for per-wallet activity
└── config/
    └── db.ts                 MongoDB connection
```

Stack: TypeScript · Node.js · `@polymarket/clob-client` · `ethers` · `mongoose` · `axios`

---

## Contributing

If you've improved execution speed, sizing logic, or added PnL reporting — open a PR. The bot gets better when people who run it make it better.

---

<div align="center">

*Car is trading right now.*
*The only question is whether your bot is running.*

**[⭐ Star this repo](https://github.com/LemnLabs/polymarket-trading-bot)** · **[View Car's wallet](https://polymarket.com/@Car?tab=activity)**

ISC © [LemnLabs](https://github.com/LemnLabs)

</div>
