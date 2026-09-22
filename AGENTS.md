# Base44 Dev Notes — Fomo Trading Bot

## What this is
A **Telegram bot** (Telegraf) for Solana token trading (Jupiter swaps, sniper TP/SL,
multi-wallet, referrals, admin broadcast). It is **not a web app**.

The only HTTP surface is a trivial health-check server on port 3000 that returns
`OK` (kept for Railway-style health checks). The preview will show `OK`, not a UI.

## Stack
- Node 18 (`engines` pinned in package.json), entry point `snipe.js`.
- Redis (optional; falls back to in-memory storage if `REDIS_URL` is unset).
- Solana web3.js + spl-token, Jupiter lite-api, Telegraf.

## Running
```
docker compose -f docker-compose.base44.yml up -d --build
docker compose -f docker-compose.base44.yml logs -f app
```
Dependencies install at container start (`npm install --omit=dev`); source is
bind-mounted so edits apply after `docker compose restart app`.

## Required secret
- `BOT_TOKEN` — Telegram bot token from @BotFather. **Required at boot.**
  Without it the process prints `BOT_TOKEN not set. Exiting.` and exits(1).
  With an *invalid* token, `bot.launch()` fails against the Telegram API and the
  catch handler also exits(1). A real token is needed for the bot to stay up.

## Other env vars (all have sensible defaults)
`SOLANA_RPC`, `ADMIN_CHAT_IDS`, `COMMISSION_WALLET`, `COMMISSION_PERCENTAGE`,
`JUPITER_API`, `REDIS_URL`, `PORT`. See `.env.example`.

## Verifying it works
- `curl http://localhost:3000/` → `OK` (health check up).
- `docker compose logs app` → `Bot running.` confirms a successful Telegram launch.
