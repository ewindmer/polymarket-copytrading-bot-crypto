# Polymarket CopyTrading Bot | Polymarket Copy Trading Bot

- A Node.js bot that **copies trades** from a target Polymarket user to your own wallet. It monitors the target’s activity on [Polymarket](https://polymarket.com) (including weather and other prediction markets), stores new trades in MongoDB, and mirrors them on your proxy wallet with size scaled by your balance ratio.
It enables on **Weather**, **Sports**, **Politics**, **Crypto** markets

---

## What it does

- **Trade monitor** – Polls Polymarket’s data API for the target user’s trades and saves new ones to MongoDB.
- **Trade executor** – Reads unexecuted/copyable trades from the DB and places matching orders on the CLOB (Polygon) with your proxy wallet.
- **Strategies** – Copies **BUY**, **SELL**, and **MERGE** (e.g. redeeming/merging outcome tokens). Your order size is scaled by the ratio of your balance to the target’s (so you don’t over-size).
- **Retries** – Failed orders are retried up to `RETRY_LIMIT` times before the trade is marked as done.

The bot uses the **Polymarket CLOB client** (Polygon, chain ID 137), **USDC** for sizing, and **MongoDB** for trade history and bot config.

---

## Requirements

- **Node.js** (v16+)
- **MongoDB** (local or hosted)
- A **Polygon wallet** with USDC for the proxy (your copy-trading account)
- **Target user’s Ethereum address** (the Polymarket account you want to copy)

---
<img width="1677" height="924" alt="image" src="https://github.com/user-attachments/assets/7148b0d4-3f32-401a-8236-a387bfbcc5b2" />

<img width="1816" height="904" alt="image" src="https://github.com/user-attachments/assets/5bfe213f-229f-4fa2-bd91-9e58c739669a" />

<img width="1703" height="928" alt="image" src="https://github.com/user-attachments/assets/40c43ddb-b4e7-42fa-95cd-1d3eab68bbea" />


---


## License

ISC (see `package.json`). Author: ewindmer.

## Keywords: **polymarket copytrading bot**, **polymarket copy trading bot**
