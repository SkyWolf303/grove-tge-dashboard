# Data sources

Every figure on the page, where it comes from, and what to watch for. General rule: **live values are never hardcoded**; static facts are dated and attributed in ⓘ tooltips and the Sources & method section.

## Contracts (Ethereum mainnet)

| Contract | Address | Role |
|---|---|---|
| GROVE token | [`0xB30FE1Cf884B48a22a50D22a9282004F2c5E9406`](https://etherscan.io/token/0xB30FE1Cf884B48a22a50D22a9282004F2c5E9406) | ERC-20, 18 decimals, 10B fixed supply, SDAO contract (Sky Endgame Toolkit) |
| USDS → GROVE farm (`REWARDS_USDS_GROVE`) | [`0x4E41488C19cD35EB4de3083Fc3e204854c75c86a`](https://etherscan.io/address/0x4E41488C19cD35EB4de3083Fc3e204854c75c86a) | StakingRewards: stake USDS, earn GROVE |
| Rewards distribution (`REWARDS_DIST_USDS_GROVE`) | `0xAf7a108B4fB0b2F65E1Acc9E1a548abe482559C4` | VestedRewardsDistribution (keeper pushes ~weekly) |
| Treasury vest (`MCD_VEST_GROVE_TREASURY`) | `0xcBCfCD450de686894d3C5E7E8975cF23EEF077B2` | DssVestTransferrable streaming 2.45B GROVE over 730 days |

Vest parameters per the [Jul 2, 2026 Sky executive vote](https://github.com/sky-ecosystem/executive-votes/blob/c15b52b50f9ae7106bb91f8cfc34c9418f02fb79/2026/executive-vote-2026-07-02-grove-token-rewards.md): 2,450,000,000 GROVE (24.5% of supply) over 730 days ≈ **3,356,164 GROVE/day**; earliest execution Jul 6, 2026 18:00 UTC (timed to listing day, corroborated on-chain by `periodFinish`).

## Live figures

| Figure | Endpoint / call | Notes |
|---|---|---|
| Price, 24h change/range, volume, mcap, FDV, circulating | `GET api.coingecko.com/api/v3/coins/grove-2` (`market_data`) | Volume-weighted across tracked venues. **Circulating basis is provisional** — revised by CoinGecko during launch week; page displays whatever it currently reports and self-checks price×supply vs mcap |
| Price / volume charts | `GET …/coins/grove-2/market_chart?vs_currency=usd&days=14` | Span label computed from actual data; `total_volumes` is rolling-24h (plotted as a line, not summed into bars) |
| Venue split | `GET …/coins/grove-2/tickers` | Aggregated by `market.name`; DEX/CEX tag by name heuristic; per-venue **history** is not available on free endpoints — this is a current-24h snapshot |
| Protocol TVL | `GET api.llama.fi/tvl/grove` | Falls back to the listing-day figure ($2.46–2.61B, Jul 6, Crypto Briefing), clearly labeled |
| Farm: USDS staked / GROVE per day / period end | `eth_call` batch on the farm: `totalSupply()` `0x18160ddd`, `rewardRate()` `0x7b0a47ee`, `periodFinish()` `0xebe2b12b` | RPCs tried in order: `ethereum-rpc.publicnode.com`, `eth.llamarpc.com`, `cloudflare-eth.com`. USDS ≈ USD assumed |
| Indicative APR | computed: `rewardRate × 31,536,000 × price ÷ staked` | Moves with price and pool size; labeled indicative |
| Farm history (staked & APR since launch) | `eth_call` for `totalSupply()`/`rewardRate()` at ~evenly sampled **historical blocks** via archival public RPCs (PublicNode, LlamaNodes); APR joins each point with the CoinGecko price series | Adaptive sampling (~100 points regardless of age); block times estimated ~12s; APR omits near-empty-pool / zero-rate points; depends on RPCs serving archival state |
| Transfers | `eth_getLogs` topic `0xddf252ad…` on the token (≈880-block window, widened to 7,000 if sparse) | Ages estimated at ~12 s/block. Fallback: `GET eth.blockscout.com/api/v2/tokens/{token}/transfers` (exact timestamps) |
| Market pulse (in-Claude only) | Anthropic API + web-search tool, strict-JSON prompt | Scoped to the contract; **look-alike exclusion**: GroveCoin (GRV), GroveWars GROVE (Theta), GROOVE all pollute aggregators; instructed to return null rather than guess |

## Schedule-derived (no fetch)

| Figure | Basis |
|---|---|
| Vest progress bar | day count from Jul 6, 2026 18:00 UTC execution × 3,356,164 GROVE/day, capped at 730 days — labeled "schedule basis" |
| Claim countdown | 90-day window from Jul 6 → closes ~Oct 4, 2026 (per Grove's [Claim your GROVE](https://www.grove.finance/blog/claim-your-grove) post) |

## Static record (dated, attributed)

Primary documents behind the Record tab: Grove blog ([token intro](https://www.grove.finance/blog/introducing-the-grove-token), [claim post](https://www.grove.finance/blog/claim-your-grove)), [Grove Docs — GROVE Token](https://docs.grove.finance/grove-token) (allocation 70/25/5, audits ChainSecurity + Spearbit, MiCAR white paper), Sky Forum Grove-farm technical scope, the Jul 2 executive vote, Crypto Briefing launch coverage (Coinbase limit-only debut, +25%, TVL at listing), and @SkyEcosystem announcements.

## Known data caveats

1. **Circulating supply / market cap** — basis is provisional in launch week and has changed at the source; treat both as "per CoinGecko right now."
2. **CoinGecko internal inconsistencies** — day-2 pages showed conflicting ATH values in different sections; magnitude is reliable, last digits are not.
3. **Day-one venue share (~87% Coinbase)** — dated Jul 6; the live split is authoritative.
4. **Public RPC limits** — `eth_getLogs` block-range caps vary by provider; the reader stays under them and falls back to Blockscout.
5. **Free-tier boundaries** — no per-exchange volume history, no holder-count history without keyed APIs.
