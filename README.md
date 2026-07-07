# Grove TGE Dashboard

A live, single-file dashboard tracking the launch of **GROVE** — the token of [Grove Protocol](https://www.grove.finance/), the Sky Ecosystem Prime Agent for institutional credit — from its token generation event on **July 6, 2026**.

**Live:** https://skywolf303.github.io/grove-tge-dashboard/

## What it shows

**Analytics tab** (default)
- Live price ledger: GROVE/USD, 24h change and range, volume, market cap, FDV
- Price and 24h-volume charts since listing, with 24h low/high markers
- Circulating supply (live, never hardcoded — see caveat below), protocol TVL, and Mcap/TVL
- 24h volume by venue, aggregated by exchange with CEX/DEX tags
- Latest on-chain GROVE transfers, read from the token contract
- Farm analytics: USDS staked, GROVE streamed/day, indicative APR, reward-period end, vest progress, claim-window countdown

**Token & launch record tab** (`#record`)
- Launch timeline (Grove Points → Coinbase roadmap → listing day)
- Token facts, genesis allocation (70/25/5), audits, contract
- Protocol fundamentals and how GROVE reaches holders
- Farm mechanics (2.45B GROVE / 730-day vest) and a curated activity log
- Full sources & methodology

## How it works

One HTML file. No build step, no framework, no API keys. Data loads client-side from public sources with tiered fallbacks, so the page degrades gracefully instead of breaking. Theme follows the viewer's system light/dark setting.

| Figure | Primary source | Fallback |
|---|---|---|
| Price, volume, mcap, FDV, supply | CoinGecko public API (`grove-2`) | Claude web search (inside Claude only) |
| Charts, venue split | CoinGecko market-history / tickers | labeled placeholder |
| Protocol TVL | DeFiLlama (`grove`) | listing-day snapshot (labeled) |
| Farm figures | On-chain reads via public Ethereum RPCs | Claude web search (inside Claude only) |
| Transfers | On-chain `Transfer` logs via public RPCs | Blockscout REST |

Full details: [docs/DATA-SOURCES.md](docs/DATA-SOURCES.md) · [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) · [docs/MAINTENANCE.md](docs/MAINTENANCE.md) · [CHANGELOG.md](CHANGELOG.md)

## Known caveat: circulating supply is provisional

For a days-old token, data providers refine the circulating-supply basis; CoinGecko revised GROVE's figure during launch week. The dashboard therefore **displays whatever basis CoinGecko currently reports**, never asserts one statically, and flags a "basis mismatch" if price × circulating stops matching the reported market cap.

## Updating

Edit `index.html`, commit to `main` — GitHub Pages redeploys automatically in about a minute. See [docs/MAINTENANCE.md](docs/MAINTENANCE.md) for the post-deploy checklist and the constants that need occasional review.

## Disclaimer

Informational only; nothing here is investment advice or an offer of any kind. Live figures come from public third-party APIs and public RPC endpoints and can be delayed, approximate, or incomplete — verify against primary sources before relying on any figure.

Built with [Claude](https://claude.com) (Anthropic). Data: CoinGecko, DeFiLlama, Blockscout, public Ethereum RPCs, Grove Docs, Sky governance repos.
