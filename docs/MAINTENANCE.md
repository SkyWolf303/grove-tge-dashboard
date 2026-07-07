# Maintenance

## Updating the dashboard

1. Edit `index.html` (it is the entire app).
2. Commit to `main` — GitHub Pages redeploys automatically in ~1 minute at
   https://skywolf303.github.io/grove-tge-dashboard/
3. Hard-refresh (Cmd/Ctrl+Shift+R) — browsers cache aggressively.

Commits can be made from the GitHub web UI (edit / upload with the same filename) or via the Contents API with a fine-grained PAT scoped to **this repo, Contents: Read and write only**. Delete tokens after use.

## Post-deploy checklist

- Status bar reads `Live · CoinGecko · updated …` on the hosted page
- Ledger numbers populated; market-cap subtitle shows a live circulating figure (no mismatch flag)
- Both charts render with a sensible "since listing · Nd" label and low/high markers
- Venue split shows aggregated rows with percentages
- Transfer list populated (RPC or Blockscout note)
- Farm strip populated; vest bar and claim countdown sensible
- Toggle OS light/dark — both themes legible
- `#record` tab renders and deep-links

## The curated activity log

`Token & launch record → Farm mechanics & activity → Activity log` is intentionally manual: add a `<li>` with date, event, and note after each weekly check-in (pool size, effective rate, claim uptake). Keep entries dated and sourced.

## Constants that need occasional review

All live in the inline `<script>` of `index.html`:

| Constant | Value | Revisit when |
|---|---|---|
| `VEST_EXEC_S` | `1783360800` (Jul 6 2026 18:00 UTC) | never (historical) |
| `VEST_PER_DAY` / `VEST_DAYS` | `3356164` / `730` | only if governance changes the stream |
| `CLAIM_END_MS` | ~Oct 4 2026 | if Grove extends the window; after close, the line auto-switches to "closed" |
| `RPCS` | 3 public endpoints | if a provider dies or throttles |
| CoinGecko ID | `grove-2` | never, barring a relist |
| DeFiLlama slug | `grove` | if the fallback text shows instead of a live TVL, verify the slug |

Static copy that is deliberately **dated** (do not "refresh" it, it is a record): listing-day TVL, ~87% day-one Coinbase share, launch-log entries.

## Future additions (parked, with why)

- **Claim-uptake tracker** — needs the claim/distributor contract address, not yet published; once known, `claimed vs allocated` is one `eth_call`.
- **Holder growth / top-holder concentration** — no keyless historical feed; Blockscout `/api/v2/tokens/{addr}` gives a current holder count if a snapshot-only stat is acceptable.
- **GROVE vs SPK at equal days-since-TGE** — CoinGecko `market_chart` on `spark-2`-equivalent ID; needs the SPK listing date pinned.
- **Farm TVL/APR sparklines** — requires storing snapshots (this page is stateless by design); a tiny GitHub Action committing a JSON time series would do it.

## Troubleshooting

- **Ledger dashes on the hosted page** — CoinGecko rate-limited the IP (free tier ~/min); Refresh after a minute.
- **"basis mismatch" flag showing** — CoinGecko's mcap and circulating are momentarily out of sync or the basis changed again; verify on the CoinGecko page, no code change needed.
- **Farm strip zeros** — see the on-page note; check vote.sky.money for spell status.
- **Transfers empty** — both RPCs and Blockscout unreachable (rare); the card links to Etherscan.
- **In-Claude preview shows placeholders** — expected; sandbox blocks third-party APIs. The hosted page is authoritative.
