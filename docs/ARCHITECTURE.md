# Architecture

The dashboard is a **single self-contained HTML file** (`index.html`): inline CSS, inline vanilla JS, Google Fonts as the only external asset. No build step, no framework, no bundler, no API keys. This is deliberate — the file can be hosted on any static host, opened locally, or rendered as a Claude artifact, and updating it is a one-file commit.

## Layout

- **Header** — title + "listing certificate" seal (SVG), decorative vein motif
- **Tabs** — `Analytics` (default) and `Token & launch record`; state mirrored to `location.hash` (`#analytics` / `#record`) so links can deep-link a tab
- **Analytics panel** — controls/status bar, live ledger, AI market-pulse card (in-Claude only), market analytics section (charts, supply/TVL/venues, transfers), farm analytics section (live strip, vest progress, claim countdown)
- **Record panel** — launch log, token facts + allocation, fundamentals, farm mechanics + curated activity log, sources & method
- **Footer** — canonical links + disclaimer + last-refresh stamp

## Theming

All colors are CSS custom properties on `:root`, with a full light-palette override under `@media (prefers-color-scheme: light)` and `color-scheme: dark light` declared. Derived UI (skeleton shimmer, bar segments, chart fills, tooltips, SVG strokes) reads the same variables, so both themes stay coherent with zero JS.

## Data layer

Everything loads client-side on page load and on **Refresh**. The design rule throughout: *try the precise source first, fall back to a labeled approximation, and show an honest empty state rather than a wrong number.*

```
fetchLive()
├── fetchCoinGecko()        → ledger numbers + circulating   (primary)
├── fetchCharts()           → price & volume series           (fire-and-forget)
├── fetchVenues()           → per-exchange 24h split          (fire-and-forget)
├── fetchLlamaTvl()         → live protocol TVL + Mcap/TVL    (fire-and-forget)
├── renderSchedules()       → vest progress + claim countdown (pure date math)
├── fetchFarmOnchain()      → farm strip via eth_call batch
├── fetchTransfers()        → Transfer logs via RPC → Blockscout fallback
└── fetchClaude()           → market-pulse narrative; fallback numbers/farm
                              figures when the direct paths are blocked
```

Failure handling: every fetch is independently caught; the status bar names whichever source actually produced the ledger; per-widget placeholders explain *why* something didn't load (e.g. sandbox blocks). If both market paths fail, the ledger shows dashes and an error card — the static record is never affected.

## Environment behavior

| Environment | Market data | Charts/venues/TVL | On-chain (farm, transfers) | Market pulse |
|---|---|---|---|---|
| **GitHub Pages / any static host / local file** | CoinGecko direct | CoinGecko + DeFiLlama direct | public RPCs (Blockscout fallback) | hidden (Anthropic API unavailable) |
| **Claude artifact preview** | Claude web search (sign-in) | labeled placeholders | Claude web search fallback for farm; transfers placeholder | shown |

The hosted page is the full experience and requires no login of any kind.

## Charts

Hand-rolled SVG (no chart library): area + line path, three horizontal gridlines with value labels, first/last date labels, optional dashed marker lines (used for the 24h low/high), and a mouse-move hover with nearest-point lookup feeding a fixed-position tooltip. Series come from CoinGecko `market_chart` (14-day request, clamped by listing age; the label shows the real span).

## Self-checks

- **Basis mismatch flag:** if `price × circulating` diverges >25% from the reported market cap, the market-cap subtitle appends "basis mismatch — verify at source". This exists because CoinGecko revised GROVE's circulating basis during launch week.
- **Keeper-lapse alert:** if the farm's `periodFinish` is in the past, the farm note warns that the weekly distribution push is overdue.
- **Zero-state explanation:** if the farm contract reads all zeros, the note explains the spell-execution timing instead of looking broken.
