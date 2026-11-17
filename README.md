[![make.fun](https://make.fun/badge/btc-race-24h-20261117-0603.svg)](https://make.fun/bounty/btc-race-24h-20261117-0603?utm_source=github&utm_medium=readme&utm_campaign=bounty)
[![rank](https://make.fun/badge/btc-race-24h-20261117-0603/Mansyur0523/makefun-btc-race-17nov/rank.svg)](https://make.fun/bounty/btc-race-24h-20261117-0603/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=bounty)
[![reward](https://make.fun/badge/btc-race-24h-20261117-0603/Mansyur0523/makefun-btc-race-17nov/reward.svg)](https://make.fun/bounty/btc-race-24h-20261117-0603/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=bounty)

# Dip-Recovery cbBTC Bot — make.fun BTC Race 24h (17 Nov 2026)

A bot that trades Coinbase Wrapped BTC (cbBTC) against USDC, minute by minute,
on 1-minute on-chain bars, with $1,000 of paper money. Built for the
[make.fun BTC race](https://make.fun/bounty/btc-race-24h-20261117-0603).

## Idea

On 1-minute bars this market mean-reverts rather than trends: after a slide of
50 bps or more over an hour, the next hour drifts back up on average, while
chasing short bursts only pays the ~26–37 bps round-trip cost and loses. So the
bot does very little:

- **Buy** when the close has fallen at least 70 bps over the last 60 bars **and**
  the last 15 bars have already turned up more than 15 bps (the dip is
  stabilising, not still falling).
- **Size 65% of cash** — a full-size fill moves its own price about 13 bps on
  this pool, so smaller size is cheaper per trade.
- **Sell** the whole position at +120 bps on cost, −150 bps stop, a 70 bps
  give-back from the peak while in profit, or after 300 bars held.
- **Cooldown 60 bars** after a sale, so it never buys straight back into the
  same dip.

## Dev-window result (published 24h window in the kit)

- Net PnL: **+$13.33** on $1,000 (dev gate: > $0 ✓)
- Trades: 6 fills (3 round trips), fees $1.58
- Max drawdown: 0.93% (guardrail ≤ 25% ✓)
- Sharpe 1.13; deterministic — two runs produce byte-identical action logs

## Run it

Python 3.13, standard library only. Speaks `agent-fun/bars-replay/v1`
(newline-delimited JSON on stdin/stdout):

```sh
python3 main.py
```

`main.py` is the entrypoint; `vendor/makefun_bars.py` is the kit's SDK shim,
copied byte-for-byte. No network, no wall clock, no randomness — the only
inputs are the `hello` message and the batches the harness sends.
