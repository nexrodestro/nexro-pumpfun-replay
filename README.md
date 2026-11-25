# nexro-pumpfun-replay — rq-ladder rider

Paper trading bot for make.fun competition
**u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122**
("Pump.fun Replay Sprint — Paper Bot, Real Ranking"). Pot $2, top 3 paid.
Free entry, paper money only — nothing is traded for real.

## Strategy

Buy the real-SOL cross: a coin becomes an entry when its real SOL crosses
upward through 8 SOL with dispersed buyers (≥8 distinct buyers in the last
40 trades, no single buyer over 35% of buy flow), net flow positive,
creator never sold, and price near its recent high. Pyramid into strength
as real SOL climbs the 16 / 32 rungs, only while the position is above
water. Exits: +45% take profit, −15% stop, trailing stop once +20%,
dump-flow exit, stale exit, 2000-slot max hold. A drawdown guard halts
new entries once equity is 25% off its peak.

## Measured (dev window, `grade-local --driver process`)

- Final (H2): net PnL **+0.479 SOL**, 126 round trips, 109 coins, win rate
  27%, max drawdown 4.5%, fees 0.70 SOL, 285 fills, 12 rejected.
  90% bootstrap interval [−0.66, +1.59] SOL. Action log byte-identical
  across the two graded executions.
- Stress (20 rebuilt copies of the dev window): median −0.96 SOL, p10
  −1.54, p90 −0.53, 0/20 positive — the dev score leans on a few coins
  (top coin +0.38, top 3 +0.88 of +0.48 net). Honest read: on a fresh
  window this is a near-breakeven diversified ticket, not a proven edge.
  The final window is new 24h data, so dev rank is a proxy only.
- Sweep history (35 configs graded): v1 −1.145 → TP25/SL10 −0.781 →
  loose entry timing −0.176 → SL08 + big size +0.281 → TP22 +0.479.
  Partial-TP + ride variants rejected on dev (v2 −0.261, v2b −0.196).
- Kit pilots on the same window: momentum −9.49, reversion −4.00,
  sniper −0.47. This bot beats all three pilots on dev PnL.

## Why this shape

Measured on the frozen dev window before building: a random entry loses
~−23% net over 300 slots (most coins bleed out). Coins whose real SOL
first crosses 10 peak at a median 1.41x the cross price within 3000 slots
(41% reach ≥1.5x), while their last-trade price sits at a median 0.56x —
the move is real but brief, so take profit fast and cut losers fast.
P(complete) rises steeply with real SOL (10% at 10 SOL, 46% at 40,
74% at 60) and completed coins run 5.7–14.5x from create, so size
pyramids up the real-SOL ladder instead of going all-in at the cross.

## Run

The grader runs `node main.ts`. No dependencies, no build step, no I/O:
the bot is a pure function of the batches the harness sends, so its two
graded executions produce byte-identical action logs.
