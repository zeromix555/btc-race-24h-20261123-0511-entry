[![make.fun · btc-race-24h-20261123-0511](https://make.fun/badge/btc-race-24h-20261123-0511.svg)](https://make.fun/bounty/btc-race-24h-20261123-0511?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261123-0511)
[![rank](https://make.fun/badge/btc-race-24h-20261123-0511/zeromix555/btc-race-24h-20261123-0511-entry/rank.svg)](https://make.fun/bounty/btc-race-24h-20261123-0511/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261123-0511)
[![reward](https://make.fun/badge/btc-race-24h-20261123-0511/zeromix555/btc-race-24h-20261123-0511-entry/reward.svg)](https://make.fun/bounty/btc-race-24h-20261123-0511?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261123-0511)

# Protected Hold — cbBTC 24h race bot

Entry for the make.fun competition `btc-race-24h-20261123-0511` — a bot
that trades Coinbase Wrapped BTC (cbBTC) on 1-minute on-chain prices
with $1,000 of paper money (simulation only, no real money).

Bounty page: https://make.fun/bounty/btc-race-24h-20261123-0511

## Strategy

A 24h BTC window is either a grind up or a grind down, and the replay
charges about 33 bps per full-size round trip, so the bot trades rarely
and lets the trend do the work:

- buys all-in on the second scored bar and holds, protected by a
  **120 bps trailing stop** below the peak close since entry;
- if the stop fires, it stays flat until a sharp **5-bar dip of 80+
  bps** that is **not part of a waterfall** (30-bar return above
  -100 bps) — that snap-back pocket is bought all-in, with its own
  50 bps trailing stop, 60 bps hard stop and 60-bar max hold;
- takes one **10%-of-cash probe at scored bar 1320** if nothing ever
  fired, to stay above the one-trade guardrail;
- blocks new entries in the last 6 scored bars.

All decisions are exact bigint comparisons on closes and fill prices —
no floats, no clock, no environment, no randomness. At most one order is
ever in flight; a new decision waits for the previous order's fill.

## Backtest (kit dev window, exact fill model)

| bot | net PnL | trades | max DD |
| --- | --- | --- | --- |
| this bot | **+$24.50** | 4 (2 round trips) | 1.87% |
| buy-and-hold | +$32.17 | 2 | 1.98% |
| dip-only (80 bps / 5 bars) | -$8.79 | 4 | 1.07% |
| EMA 8/21 cross | -$118.59 | 80 | 12.12% |

The same bot on the previous round's falling dev window loses only
-$1.87 where buy-and-hold loses -$52.59, and on both windows with
sign-reversed returns it is the best of the tested family (worst
-$17.77 vs buy-and-hold -$52.57). Neighbouring parameters (trail
120–140 bps, dip 75–85 bps, range filter -100 to -120 bps) all stay in
the same band, so nothing here is a knife-edge fit.

## Files

- `main.ts` — the bot (protocol loop + strategy, Node 22, standard
  library only, no vendored SDK)
- `LICENSE` — MIT
