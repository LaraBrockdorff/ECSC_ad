# Malta coach desk

A local, automatically refreshing ECSC A/D dashboard. Requires Python 3.10+; no packages or build step.

```sh
python server.py
```

Open http://127.0.0.1:8765. Keep the terminal running; Ctrl+C stops it.

For a labelled synthetic preview:

```sh
python server.py --demo --port 8766
```

## What coaches see

- Malta's rank, cumulative score and points achieved this round. Live standings sit directly below these metrics and show up to five teams above Malta and five below. Neighbour summaries use red when they gain on Malta, green when Malta catches up or pulls away.
- Improving / steady / slowing: compares average points gained in the recent half of the selected window with the earlier half. Changes within 5% are steady.
- Per-tick delta versus Malta in standings and the historical round ledger: their points earned that tick minus ours. If Malta earns 100 and another team earns 105, their delta is +5; 95 gives −5. The ledger identifies the team immediately above/below in each tick.
- Exact points gap to the teams immediately above and below. Gap movement compares Malta's current-round gain with each neighbour's current-round gain; positive gaps are displayed as distances. Standings also show signed score differences relative to Malta. Tied positions use the published table order.
- A score chart and simple next-10-round projection at the recent average pace. Shading represents approximate gain variability, not a calibrated probability or final-placement prediction. Missing rounds are not treated as consecutive observations.
- Service checker health, attack / defense / SLA points, successful flags captured and flags lost; historical round selector and round ledger.
- Possible attacking and victim teams on a selected service, clearly labelled as candidates. **The public API does not expose attacker–victim pairs. Captures and losses cannot prove which teams attacked Malta or which teams Malta attacked.** It also cannot show unsuccessful network attacks.

## Data and refresh

The backend polls every 30 seconds and fetches each published round once, latest first, then backfills all earlier rounds with three concurrent requests. The UI refreshes independently (15 / 30 / 60 seconds or pause). Refresh now reads the server's latest collected snapshot. Round JSON files persist in `data/`; partial history, unreachable feeds, stale syncs and a frozen scoreboard are labelled. Initial backfill may take a little time. The dashboard uses scored rounds, which lag the currently playing round.

`attack.json` contains flag identifiers, not performance scores or confirmed attack relationships. This dashboard uses the organizer's published scoreboard endpoints instead:

- `/api/scoreboard_current.json`
- `/api/scoreboard_teams.json`
- `/api/scoreboard_round_{round}.json`

API reference: https://github.com/Attacking-Lab/ecsc2026-playerlib/blob/main/openapi.yaml

The live host must be reachable from the machine running this server (including any competition network/VPN requirements).

```sh
python server.py --interval 30 --port 8765 --team Malta
python server.py --source http://your-scoreboard --cache ./alternate-data
python -m unittest -v
```

Use a distinct cache directory for each different competition/source. The server binds only to loopback. Nothing is deployed externally.
# ECSC_ad
