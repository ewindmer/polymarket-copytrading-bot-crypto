```
POLYMARKET COPY ENGINE — INSTRUMENT BRIEF
Version   1.0.0
Network   Polygon (USDC)
Language  TypeScript / Node.js 18+
State     MongoDB
Status    ACTIVE
```

---

```
INSTRUMENT
──────────────────────────────────────────────────────────────────────
Type          Automated copy-trade executor
Target        Polymarket prediction markets
Signal source Observed wallet activity — USER_ADDRESS
Execution     Polymarket CLOB API (OrderType.FOK)
Sizing model  Proportional — follower_balance ÷ source_balance × size
Latency       ~1–3s from source fill to order submission
```

---

```
TARGET WALLET
──────────────────────────────────────────────────────────────────────
Handle        Car
Profile       https://polymarket.com/@Car?tab=activity
Address       0x7C3Db723F1D4d8cB9C550095203b686cB11E5C6B
Signal type   TRADE events, deduplicated by transaction hash
```

---

```
EXECUTION MODEL
──────────────────────────────────────────────────────────────────────
Step 1   Poll  activities?user=<wallet> at FETCH_INTERVAL (default: 1s)
Step 2   Gate  Reject events older than TOO_OLD_TIMESTAMP (default: 24h)
Step 3   Gate  Reject if |current_price - source_fill| > 0.05
Step 4   Size  order_size = (follower_usdc / source_usdc) × source_size
Step 5   Post  Submit FOK order via @polymarket/clob-client
Step 6   Retry On failure, retry up to RETRY_LIMIT (default: 3)
Step 7   Log   Write detection + execution record to MongoDB
```

---

```
OPERATIONAL PARAMETERS
──────────────────────────────────────────────────────────────────────
Variable               Required  Default  Description
─────────────────────────────────────────────────────────────────────
USER_ADDRESS           YES       —        Target wallet(s), comma-sep
PROXY_WALLET           YES       —        Your execution wallet
PRIVATE_KEY            YES       —        Signing key — never commit
CLOB_HTTP_URL          YES       —        https://clob.polymarket.com
CLOB_WS_URL            YES       —        wss://clob-ws.polymarket.com
RPC_URL                YES       —        Polygon RPC endpoint
USDC_CONTRACT_ADDRESS  YES       —        0x2791Bca1f2de4661...aa84174
MONGO_URI              YES       —        MongoDB connection string
FETCH_INTERVAL         NO        1        Poll cadence in seconds
TOO_OLD_TIMESTAMP      NO        24       Max event age in hours
RETRY_LIMIT            NO        3        Max order retries
```

---

```
PERFORMANCE PARAMETERS
──────────────────────────────────────────────────────────────────────
Metric                  Value          Notes
────────────────────────────────────────────────────────────────────
Copy latency            ~1–3s          Network + API dependent
Max price deviation     0.05           Configurable in executor
Position sizing         Proportional   Source/follower balance ratio
Retry cap               3              Marks failed after limit
Deduplication           Tx hash        Idempotent — no double fills
Trade log retention     Indefinite     MongoDB, queryable

[ Fill in live PnL, win rate, and drawdown after your first run ]
```

---

```
CODEBASE MAP
──────────────────────────────────────────────────────────────────────
src/index.ts                  Bootstrap, env validation, orchestration
src/services/tradeMonitor.ts  Activity fetch + MongoDB write
src/services/tradeExecutor.ts Pending trade scan + order dispatch
src/utils/postOrder.ts        Buy / sell / merge order logic
src/utils/createClobClient.ts API key derivation + CLOB init
src/utils/getMyBalance.ts     USDC balance via Polygon RPC
src/models/userHistory.ts     Per-wallet activity collections
src/config/db.ts              MongoDB connection handler
```

---

```
RISK PARAMETERS
──────────────────────────────────────────────────────────────────────
Factor               Exposure
────────────────────────────────────────────────────────────────────
Slippage             Entry price will differ from source fill
Latency              Odds shift between source fill and your order
Source drawdown      You mirror losses as well as gains
RPC/API outage       Missed signals — no built-in alerting
Key management       PRIVATE_KEY in .env — use a dedicated wallet
Smart contract       On-chain interaction risk — Polygon/USDC

RECOMMENDED: Fund with capital sized for total loss tolerance.
             Monitor manually for first 20 trades before scaling.
             This software is not financial advice.
```

---

```
DEPLOYMENT
──────────────────────────────────────────────────────────────────────
# 1. Install
npm install

# 2. Configure
cp env.example .env
# Edit USER_ADDRESS, PROXY_WALLET, PRIVATE_KEY

# 3. Run (development)
npm run dev

# 4. Run (production)
npm run build && npm start
```

---

```
COMMANDS
──────────────────────────────────────────────────────────────────────
npm run dev        ts-node src/index.ts (hot reload)
npm run build      Compile TypeScript → dist/
npm start          Run dist/index.js
npm run lint       ESLint check
npm run lint:fix   ESLint auto-fix
npm run format     Prettier format
```

---

```
STACK
──────────────────────────────────────────────────────────────────────
Runtime     Node.js 18+
Language    TypeScript (strict: true)
Trading     @polymarket/clob-client
Wallet      ethers
HTTP        axios
DB          mongoose (MongoDB)
UX          ora, chalk

DEPENDENCIES: See package.json
LICENSE:      ISC © LemnLabs
ISSUES:       https://github.com/LemnLabs/polymarket-trading-bot/issues
```
