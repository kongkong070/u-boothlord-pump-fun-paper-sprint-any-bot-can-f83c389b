# Surge Rider — Pump.fun Paper Sprint bot

[![make.fun](https://make.fun/badge/u-boothlord-pump-fun-paper-sprint-any-bot-can-f83c389b.svg)](https://make.fun/bounty/u-boothlord-pump-fun-paper-sprint-any-bot-can-f83c389b)
[![rank](https://make.fun/badge/u-boothlord-pump-fun-paper-sprint-any-bot-can-f83c389b/kongkong070/u-boothlord-pump-fun-paper-sprint-any-bot-can-f83c389b/rank.svg)](https://make.fun/bounty/u-boothlord-pump-fun-paper-sprint-any-bot-can-f83c389b)
[![reward](https://make.fun/badge/u-boothlord-pump-fun-paper-sprint-any-bot-can-f83c389b/kongkong070/u-boothlord-pump-fun-paper-sprint-any-bot-can-f83c389b/reward.svg)](https://make.fun/bounty/u-boothlord-pump-fun-paper-sprint-any-bot-can-f83c389b)

A momentum bot for the make.fun pump.fun paper-trading replay
(`agent-fun/replay/v2`). Paper trading only — no real money, no real trades.

## Strategy

On pump.fun almost every new curve dies, but the few that run do so on visible,
accelerating buy flow from many distinct wallets. Surge Rider only buys a curve
once its tape already shows that flow — mature enough to have survived the
launch dump, rising, near its recent high, with the newer half of the window
pulling in more SOL than the older half — and it starts small (0.06 SOL).
Winners get pyramided into while the price keeps stepping up and the position
is in profit, up to 1.5 SOL per position; losers are cut fast by a stop loss
(-35%) and a trailing stop, and nothing is held long. A pacing probe keeps the
run above the guardrail (a round trip of ≥ 0.01 SOL on at least 20 coins) even
in a quiet stretch.

Tuned with the kit's `grade-local` (the real harness) on the dev window: net
-0.49 SOL, max drawdown 0.10, 524 fills over 98 coins, 179 round trips — against
-9.38 SOL for the house pilot momentum bot on the same window. (The kit's sim port
overestimates badly — it called the first draft breakeven where the real harness
said -1.73 SOL — so all tuning was done on grade-local, sweeping entry selectivity
and stake size.) The dev edge is thin and the final window is different data, so
the defence is the structure (trade only the strongest flows, keep stakes small,
cut losers, let runners run), not the exact decimals.

## Layout

- `main.ts` — entrypoint; the grader runs `node /bot/main.ts` (Node 22).
- `strategy.ts` — the strategy, written from scratch.
- `vendor/makefun-bot.ts` — the kit's SDK shim, byte-identical to
  `sdk/ts/makefun-bot.ts` (vendored, excluded from authorship counts).

Deterministic by construction: no clock, no environment, no randomness;
iteration only over insertion-ordered maps fed by the batch stream. The bot
is executed twice by the grader and the two action logs must match byte for
byte.
