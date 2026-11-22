# makefun-token-radar-01

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/Keso1234/makefun-token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/Keso1234/makefun-token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

A token launch radar for [make.fun's token-radar-01 bounty](https://make.fun/bounty/token-radar-01): it reads a sealed snapshot of every protocol DefiLlama tracks, works out which ones still record no token and are not a wrapped or staked product of one that trades, and ranks them by what their launch would be worth — then hands in a static dashboard with the board, a detail view for each protocol, and the steps to farm it.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json|directory>
```

That writes `out/board.json` (the ranked board, one farm plan per row), `out/data.js` (the dashboard's data), `out/dashboard/` (the static page, no network), `out/screenshots/` and `out/dossier.json` (the judge's summary, built with the vendored make.fun library).

## The model (radar-2)

Implied value follows only from the snapshot's own figures, priced off the market the snapshot itself records:

```
multiple   = median(mcap_usd / tvl_usd) over live-token protocols in the same
             category (at least 3 of them, else the global median), clamped to [0.05, 1.50]
growth     = clamp(change_7d, -60, 120) per cent
growthMult = 1 + (growth / 100) x 0.5
maturity   = 0.6 + min(age_days, 1095) / 1095 x 0.4   (age unknown: 365)
breadth    = 1 + 0.02 x min(max(chains - 1, 0), 5)
implied    = max(1, round(tvl_usd x multiple x growthMult x maturity x breadth))
```

A Dex earns its multiple from Dexs that trade, a lender from lenders that trade. No protocol name is ever special-cased: the same function scores every row, and the multiples are recomputed from the snapshot on every run. The board covers the whole 40-protocol watchlist plus every other rankable pre-token protocol below the floor — bridges, liquid staking, CEX rows and versioned pools are excluded by the watchlist's own rule, since a wrapped product is not a launch waiting to happen.

## Layout

- `main.ts` — the entrypoint: snapshot in, board + dashboard + dossier out
- `lib/model.ts` — the radar-2 model, the board rule and the farm plans
- `vendor/makefun-board.ts` — make.fun's dossier library, byte for byte as shipped in the kit
- `dashboard/` — the static page: `index.html`, `app.css`, `app.js`
- `screenshots/` — the board, a detail view and a filtered view

---

<sub>Entered on [make.fun](https://make.fun/?ref=taaana65&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>
