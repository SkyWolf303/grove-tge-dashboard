# Changelog

All notable changes to the dashboard. Dates are 2026.

## Jul 7 — Analytics restructure + adversarial-review fixes (`eeabcf2`)
- Two-tab layout: **Analytics** (default) and **Token & launch record** (`#record` deep link)
- Price & volume charts since listing (hand-rolled SVG, hover tooltips, 24h low/high markers, real span labels)
- Venue split aggregated by exchange with CEX/DEX tags, pair counts, and an "Other" bucket
- Latest on-chain GROVE transfers (RPC `eth_getLogs`, Blockscout fallback with exact timestamps)
- **Supply-basis fix:** removed every hardcoded "7B circulating" string; market-cap subtitle and supply card render live from CoinGecko with neutral fallbacks, provisional-basis disclosure, and an automatic price×supply vs market-cap mismatch flag
- Live protocol TVL from DeFiLlama + Mcap/TVL ratio; listing-day TVL relabeled as a dated snapshot
- Vest progress bar (day N of 730), claim-window countdown (~Oct 4), keeper-lapse alert
- Source attribution moved into ⓘ hover/focus tooltips next to each figure

## Jul 7 — Auto theme + live farm reads
- Auto light/dark via `prefers-color-scheme` (full CSS-variable palette)
- On-chain farm strip: USDS staked, GROVE/day, indicative APR, period end — read from `REWARDS_USDS_GROVE` via public RPCs
- Farm mechanics sourced from the Jul 2 Sky executive spell (2.45B GROVE / 730-day vest, execution timed to listing day)

## Jul 7 — Data layer + sourcing
- CoinGecko (`grove-2`) wired as primary market source, verified against the token contract; Claude web search as in-Claude fallback
- Per-section source attribution; Sources & method section
- Deep-research pass: genesis allocation 70/25/5, contract + audits, 90-day claim window, Grove Points timeline, MiCAR white paper

## Jul 7 — Initial release
- Single-file launch dashboard: live ledger, launch log, token facts, TVL, distribution, footer links, disclaimer
- Published to GitHub Pages
