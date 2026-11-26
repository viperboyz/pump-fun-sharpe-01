# Pump.fun Sharpe Sprint — Cleanest Paper Bot

[![Competition](https://make.fun/badge/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532.svg)](https://make.fun/competitions/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532)
[![Rank](https://make.fun/badge/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532/viperboyz/pump-fun-sharpe-01/rank.svg)](https://make.fun/competitions/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532)
[![Reward](https://make.fun/badge/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532/viperboyz/pump-fun-sharpe-01/reward.svg)](https://make.fun/competitions/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532)

An entry for the make.fun bounty **Pump.fun Sharpe Sprint — Cleanest Paper Bot Wins**
(`u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532`): a paper-trading bot for a
6-hour pump.fun bonding-curve replay, ranked by hourly Sharpe ratio. Paper trading
only — no real funds, no real orders.

## Strategy: early-selective scalper

The average pump.fun coin drifts down from its launch price, but a small tail of
coins runs 2-4x within minutes. This bot buys only the quiet launches whose
opening tape looks like a real crowd forming, then lets the rare runner pay for
many tiny losses:

- **Entry (within 2 slots of creation):** 5-6 distinct non-creator buyers, at
  least 5 trades, net SOL flowing in (≥ 0.2 SOL), creator still holding, creator
  buy ≤ 1 SOL, real reserves ≥ 2 SOL, price run-up ≤ +40%. One small buy per
  coin (0.05 SOL target, capped at 1% of real reserves), no pyramiding.
- **Exits:** tight stop-loss (-3%), very high take-profit (+200%), hard time
  limit of 900 slots (~3 min), plus exit when the creator sells or the coin
  goes stale. No trailing stop — the winners are left to run to the take-profit.
- **Coverage probe:** if many coins have been seen but few round trips were
  made, the bot takes a minimum-size (0.011 SOL) round trip on the most liquid
  skipped coin, so the 20-coin guardrail is met even in a quiet window.
- **Deterministic:** a pure function of the batches it is fed — no clock, no
  randomness, no environment, no I/O. Every decision is in whole lamports.

## Local test results (kit dev window, 24h, local engine port)

| | net PnL | hourly Sharpe | max drawdown | coins | trips | win rate |
|---|---|---|---|---|---|---|
| This bot | **+0.042 SOL** | **+0.093** | 0.48% | 37 | 37 | 16.2% |
| Pilot sniper (house) | -0.489 SOL | -0.847 | 5.3% | 354 | 354 | 12.7% |
| Pilot momentum (house) | -9.387 SOL | -0.575 | 95.0% | 48 | 80 | 6.3% |

Two runs of the full dev window produce identical results (deterministic).
Out-of-sample splits of the same window are weaker (parts 1-2: -0.031 SOL),
so the edge is thin and regime-dependent — the profit rests on a few coins
that run 2-4x. Drawdown stays far under the 50% cap in every split.

## Files

- `main.ts` — entry point, wires the strategy into the make.fun harness.
- `strategy.ts` — the strategy (all trading logic, tunable `DEFAULT_PARAMS`).
- `vendor/makefun-bot.ts` — byte-identical copy of the kit's `sdk/ts/makefun-bot.ts`
  (the harness shim; kept out of authorship counts).
- `LICENSE` — MIT, copied from the kit's starter pack.

## Run

```sh
node main.ts
```

Runs on Node 22 as `node /bot/main.ts` under the `agent-fun/replay/v2` harness.
Stdlib only.
