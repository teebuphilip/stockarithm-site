# Public JSON Schema

This is the stable public artifact contract for the static site.

Contract rules:

- every top-level public JSON file carries `schema_version`
- current version is `v1`
- additive fields are allowed within `v1`
- field removals, renames, or meaning changes require `v2`
- public files must stay teaser-safe:
  - no trade history
  - no equity curves
  - no per-algo internal breakdowns
  - no private factory/debug artifacts

## Shared Row Shape

These files share the same algo row shape:

- `leaderboard.json`
- `watchlist.json`
- `promoted.json`
- `graveyard.json`
- `daily.json` -> `top_live_ytd[]`
- `families.json` -> `families.<family>.leaders[]`

Algo row fields:

| Field | Type | Notes |
|---|---|---|
| `schema_version` | `string` | currently `v1` |
| `key` | `string` | stable composite key |
| `algo_type` | `string` | `normal` or `crazy` |
| `algo_id` | `string` | stable internal/public id |
| `name` | `string` | display name |
| `family` | `string \| null` | normalized family |
| `status` | `string` | product-layer status |
| `evidence_class` | `string` | product-layer evidence bucket |
| `rebalance_frequency` | `string \| null` | e.g. `daily`, `weekly`, `monthly` |
| `ytd_pct` | `number \| null` | strategy YTD return in percent |
| `days_running` | `integer` | days since tracking started |
| `beat_spy` | `boolean \| null` | latest YTD comparison vs SPY |
| `spy_pct` | `number \| null` | SPY YTD return in percent |
| `alpha_pct` | `number \| null` | YTD strategy minus SPY |
| `ret_30d_pct` | `number \| null` | trailing 30D return in percent |
| `spy_delta_30d_pct` | `number \| null` | trailing 30D strategy minus SPY |
| `last_snapshot` | `string \| null` | last validated snapshot date |
| `new_algo_date` | `string \| null` | original activation date |
| `comparator` | `object \| null` | baseline context for mapped sector ETF |

Comparator object:

| Field | Type | Notes |
|---|---|---|
| `primary_etf` | `string` | one of the 11 sector ETFs |
| `signals` | `object` | comparator-name keyed payload |
| `summary` | `string` | current compact status |

Comparator signal row:

| Field | Type | Notes |
|---|---|---|
| `direction` | `string` | `UP`, `DOWN`, `NEUTRAL` |
| `confidence` | `number \| null` | comparator confidence |
| `return_pct` | `number \| null` | comparator raw return basis if available |

## Top-Level Files

### `daily.json`

Purpose:

- one-file nightly public snapshot for the homepage and daily page

Fields:

| Field | Type |
|---|---|
| `schema_version` | `string` |
| `run_date` | `string` |
| `generated_at` | `string` |
| `counts` | `object` |
| `by_status` | `object` |
| `by_evidence_class` | `object` |
| `new_algos_today` | `array` |
| `top_bullish_etfs` | `array<string>` |
| `top_bearish_etfs` | `array<string>` |
| `ticker_count` | `integer \| null` |
| `signal_count` | `integer \| null` |
| `sector_summary` | `object` |
| `top_live_ytd` | `array<algo row>` |
| `rolling_30d` | `object` |

`rolling_30d` fields:

| Field | Type |
|---|---|
| `as_of` | `string \| null` |
| `spy_ret_30d_pct` | `number \| null` |
| `leaders` | `array<object>` |

### `leaderboard.json`

Purpose:

- full public leaderboard payload for the proof page

Fields:

| Field | Type |
|---|---|
| `schema_version` | `string` |
| `generated_at` | `string` |
| `counts.total` | `integer` |
| `counts.beating_spy` | `integer` |
| `algos` | `array<algo row>` |

### `families.json`

Purpose:

- family overview and top leaders per family

Fields:

| Field | Type |
|---|---|
| `schema_version` | `string` |
| `generated_at` | `string` |
| `families` | `object` |

Each family payload:

| Field | Type |
|---|---|
| `family` | `string` |
| `count` | `integer` |
| `by_status` | `object` |
| `by_evidence_class` | `object` |
| `leaders` | `array<algo row>` |

### `watchlist.json`, `promoted.json`, `graveyard.json`

Purpose:

- product buckets with the shared algo row shape

Fields:

| Field | Type |
|---|---|
| `schema_version` | `string` |
| `generated_at` | `string` |
| `algos` | `array<algo row>` |

## Benchmarks

### `benchmarks/spy.json`

| Field | Type |
|---|---|
| `schema_version` | `string` |
| `symbol` | `string` |
| `available` | `boolean` |
| `generated_at` | `string` |
| `ytd_return_pct` | `number \| null` |
| `rolling_30d_return_pct` | `number \| null` |
| `source` | `string \| null` |

### `benchmarks/qqq.json`

Same shape as SPY plus:

| Field | Type |
|---|---|
| `reason` | `string` |

## Signal Teaser Files

Files:

- `signals/index.json`
- `signals/<ticker>.json`

Purpose:

- public teaser payload only
- enough for lookup pages and summary pages
- not enough to reconstruct the private per-algo breakdown

Fields:

| Field | Type |
|---|---|
| `schema_version` | `string` |
| `ticker` | `string \| null` |
| `sector` | `string \| null` |
| `sector_etf` | `string \| null` |
| `generated` | `string \| null` |
| `lab_started` | `string \| null` |
| `composite` | `string \| null` |
| `composite_label` | `string \| null` |
| `composite_color` | `string \| null` |
| `bullish` | `integer \| null` |
| `bearish` | `integer \| null` |
| `neutral` | `integer \| null` |
| `bullish_pct` | `integer \| null` |
| `total_signals` | `integer \| null` |
| `total_active` | `integer \| null` |
| `disclaimer` | `string` |
| `detail_level` | `string` | currently `public_teaser` |
| `premium_breakdown_available` | `boolean` |

## Source of Truth

Generated from:

- `data/product/*.json`
- `docs/signals/*.json`
- `docs/leaderboards/rolling_30d.json`
- `data/rank_history.csv`
- `docs/comparison/today.json`

Generator:

- `scripts/build_public_artifacts.py`
