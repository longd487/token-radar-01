# Token Launch Radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/longd487/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/longd487/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

A token launch radar for the make.fun bounty
[`token-radar-01`](https://make.fun/bounty/token-radar-01): it reads a sealed
snapshot of the protocol universe DefiLlama tracks, works out which
protocols still record no token, and ranks them by what their launch would
be worth — then hands in a web dashboard with the board, a detail view for
every protocol, and the steps to farm it.

## The model (radar-2)

Each pre-token protocol is ranked by its **expected launch value**:

```
expected = TVL x category multiple x growth multiplier
                 x maturity multiplier x launch probability
```

Every figure is calibrated from the snapshot itself at run time — nothing
is hardcoded per protocol:

- **Category multiple**: the category's median mcap/TVL among live
  protocols, shrunk toward the global median (0.214 here). A flat multiple
  would ignore that Liquid Staking trades near 0.04x TVL while bridges
  trade near 0.6x.
- **Growth multiplier**: the protocol's 7-day TVL change minus its
  category's median 7-day change, clamped and shrunk — a +5% week means
  little in a +5% category.
- **Maturity multiplier**: saturating in age with no token,
  `0.7 + 0.3 x (1 - exp(-age/540d))`. Older no-token protocols are likelier
  to launch, but the curve flattens instead of crediting every extra year.
- **Launch probability**: the category's tokenisation rate,
  live / (live + pre-token), shrunk toward the global rate and clamped.
  Risk Curators sit at 1/35 live in this snapshot while Lending sits near
  half: the same TVL is not the same launch.

The board covers the snapshot's whole 40-protocol watchlist and adds 138
early rows below the $50M floor, for 178 rows. Live-token protocols are
kept apart and shown with their market values as reported, never mixed
into the ranked board. A protocol "records no token" in the snapshot's
sense — the record is not proof it has none.

## Run it

Node.js 22.18 or later, standard library only. The snapshot lives outside
the repository (the grader mounts it at `/input/snapshot.json`):

```sh
mkdir -p out
MAKEFUN_OUTPUT_DIR=out node main.ts --input /path/to/snapshot.json
```

That writes `out/board.json` (the ranked board), `out/data.js` (the
dashboard's data), `out/dashboard/` and `out/screenshots/` (copies of this
repo's), and `out/dossier.json` (built with the vendored make.fun library
in `vendor/makefun-board.ts`).

## Layout

- `main.ts` — the entrypoint: read the snapshot, score, write the outputs.
- `lib/model.ts` — the model, the board builder, the farm-plan templates.
- `dashboard/` — the static page (no framework, no network): the board,
  a detail view per protocol, filters, and the methodology.
- `screenshots/` — the board, a detail view, and a filtered view.
- `vendor/makefun-board.ts` — the kit's dossier library, byte for byte.

## License

MIT. See `LICENSE`.

---

Entered on [make.fun](https://make.fun/?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards
