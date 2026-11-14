# token-radar

Token launch radar — make.fun bounty entry (token-radar-01).

A dashboard that ranks pre-token protocols by implied value: what a protocol's
token would be worth if its record stayed as it is, and what a reader should do
to farm it before or after it issues one.

## The model (radar-2)

`lib/model.ts` scores every protocol from the snapshot's own figures only:

- **Category multiple** — the median market-cap / TVL of the live protocols in
  the same category, read out of the snapshot itself (fallback: the median over
  live protocols at or above the watchlist floor). No multiple is hand-picked.
- **Momentum** — the seven-day TVL change, clamped to [-50, +100]%. A falling
  week costs about twice what a rising week earns.
- **Seasoning** — `0.55 + 0.45 x (1 - e^(-age/365))`: each extra year a protocol
  runs with no token adds less than the last.
- **Reach** — `1 + 0.02` per extra chain, up to +20%.

`implied = round(TVL x category_multiple x momentum x seasoning x reach)`.
Wrapped, staked and bridged products of tokens that already trade are kept off
the board by the same rule that built the watchlist.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json>
```

`main.ts` (Node 22, standard library only) reads the sealed snapshot, writes
`board.json` and `data.js`, copies `dashboard/` and `screenshots/`, and writes
`dossier.json`. The dashboard is a static page with no network: open
`out/dashboard/index.html` from disk.

## Layout

- `main.ts` — entrypoint
- `lib/model.ts` — the model, the board and the farm plans
- `vendor/makefun-board.ts` — the vendored dossier library
- `dashboard/` — `index.html`, `app.css`, `app.js`
- `screenshots/` — the board, a detail view and a filtered view
