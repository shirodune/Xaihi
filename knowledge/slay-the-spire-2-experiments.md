---
type: Experiment Report
title: First Slay the Spire 2 runs
description: Outcomes and token-accounting lessons from external and built-in agents.
tags: [games, slay-the-spire-2, experiments, cost]
---

# First Slay the Spire 2 runs

These are exploratory attempts, not a benchmark or a model leaderboard. Characters, difficulty, starting conditions, controller implementations and cache behavior differ. We have not achieved a verified full clear.

The tested environment was Windows, game v0.111.0, STS2-Agent v0.15.0. Public notes summarize private game-state evidence, worker transcripts and gateway accounting; raw logs, credentials, machine identifiers and native saves are not published. The notes are agent-authored and not independently reproduced. See the [connection guide](slay-the-spire-2.md).

## The runs

### 1. Ironclad A10: external Opus via a dedicated Hermes session

The first external player used terminal-level MCP requests rather than Hermes's native MCP tools. It won early fights, reached the Act 1 boss, and died to Ceremonial Beast on floor 17. The boss had 122/262 HP remaining. The session was resumed in stages; it was one game, not several runs.

The worker suggested potion timing and conditional-effect mistakes in its postmortem. Those are hypotheses, not independently proven optimal-play comparisons.

### 2. Ironclad A10: external Opus via native MCP

A new dedicated Hermes session used native game tools. It stopped after Claude quota exhaustion and was subsequently abandoned with the owner's permission. This is an interrupted run, not a demonstrated combat defeat.

Repeated context compression was visible. Native MCP simplified integration but did not prevent tool results from accumulating in conversation history.

### 3. Ironclad A10: built-in Sol autoplay

We verified the built-in player could choose opening options and play cards, and that a ten-request budget stopped it. The owner then authorized continuing the same game without that cap.

The owner observed a loss; the supervisor subsequently verified autoplay had stopped with `stop_kind=run_end`. We did not preserve enough terminal game-state evidence to claim an exact death floor or opponent. No precise per-run token total is published for this attempt.

### 4. Regent A0: built-in Opus, then external Sol

This was **one run with a controller/model handoff**, not two games.

- Built-in Opus reached Act 2, floor 30, at 22/75 HP before an upstream 429 rate-limit failure stopped it.
- A fresh native-MCP Hermes session using Sol continued from that save. It defeated Spiny Toad, reached the floor-31 map at 22/75 HP and 138 gold, then stopped at the agreed checkpoint.
- With permission to finish the run, the same external session resumed, entered a rest site and fought The Insatiable.
- The run ended in defeat on floor 33, turn 6, at 0/75 HP; the boss had 92/321 HP remaining. The supervisor read back `victory=false` and `save_verified=true` after game-over progression. No new run was started.

The worker reported 38 HP and 7 Block before the final end-turn, against a listed 7x2 attack. This does not establish the death mechanism: special mechanics and the pre-death state require further review. Do not label it a damage-calculation bug from the final snapshot alone.

The earlier reward collection also added Glow without presenting a card-choice state to the worker. That behavior was recorded, not treated as a deliberate evaluated card choice.

## Measured usage

### Opus comparisons: common gateway accounting

These figures use successful requests filtered by model, experiment time window and client origin in CPA Manager Plus, cross-checked against Hermes or Mod records. Failures are reported separately. Elapsed time is the first-to-last request span, not pure inference latency; pauses may be included.

| Experiment | Successful requests | Cache read | Cache creation | Output | Total tokens | Input cache-read share |
|---|---:|---:|---:|---:|---:|---:|
| External terminal-MCP Ironclad run | 189 | 17,108,807 | 160,798 | 84,387 | 17,354,374 | 99.1% |
| External native-MCP Ironclad run | 244 | 36,173,792 | 1,997,663 | 107,784 | 38,279,743 | 94.8% |
| Built-in Regent segment | 627 | 3,841,945 | 4,590,566 | 162,061 | 8,595,826 | 45.6% |

Uncached input was respectively 382, 504 and 1,254 tokens. Request spans were approximately 29.6, 21.9 and 42.4 minutes. Gateway failure counts were respectively 3, 1 and 1.

The second external run includes **237 ordinary calls plus 7 compression calls**. Omitting compression understates both output and cache creation. The built-in segment recorded 395 accepted game actions across its 627 successful requests: a game action is not a model request.

The built-in segment processed far fewer total tokens, but created about 2.30 times as many cache tokens as the native-MCP external run. High cache-read volume and high fresh/cache-creation volume are not equivalent costs. We do not convert these numbers into dollars or subscription percentages.

### Sol handoff: Hermes session accounting

| Segment | Model calls including compression | Cache read | Uncached input | Output | Input cache-read share |
|---|---:|---:|---:|---:|---:|
| One-combat checkpoint | 33 | 3,087,232 | 185,010 | 3,282 | 94.3% |
| Resume through Act 2 defeat | 78 | 11,341,440 | 716,278 | 9,784 | 94.1% |

The final segment includes two compression calls. Sol did not expose a separate cache-creation count in these records; zero in that field is not proof that no cache was built.

An initial worker launch with an incorrect toolset selector made no game actions but consumed another 3 calls, 22,656 cache-read tokens, 14,461 uncached input tokens and 775 output tokens. This setup overhead is additional to the two rows, not hidden inside gameplay performance.

Subscription quota was shared, starting balances differed, and full pre/post quota baselines were not captured. The data cannot establish which setup consumed the smallest percentage of a five-hour subscription window.

## Cache investigation

During the built-in Opus segment, 394 of 627 successful requests read exactly 1,724 cached tokens. Only one had no cache read. Requests used one upstream account identifier, with a maximum adjacent request-timestamp gap of 12.6 seconds. Frequent account rotation or long idle expiration does not explain that pattern well.

Version-matched Mod source places fixed rules and scene guidance before the changing game state. The inspected CPA source also includes automatic cache-control handling. However, historical usage records do not contain the actual outbound request bodies, so the precise reused prefix and live breakpoint placement remain unverified.

[CLIProxyAPI issue #5730](https://github.com/router-for-me/CLIProxyAPI/issues/5730) reports a similar short-prefix cache plateau for third-party clients. Its reported versions and reproduction differ from ours. It is a diagnostic lead, not proof of a shared root cause or a verified fix. We made no gateway changes as part of this review.

## What we would change next

- Keep native game tools and one controller, but treat them as an integration choice, not a cost optimization by themselves.
- Set a small first milestone and verify actual tool availability in the worker before granting a long run.
- Reuse compact action results; avoid redundant state reads and indiscriminate metadata queries.
- Inspect context size and cache creation as well as hit percentage. A high hit rate can still accompany a very large prompt.
- Consider fresh sessions at stable room boundaries with short checkpoints; do not reset per card and destroy useful cache continuity. The best segmentation interval has not been established.
- Include compression and failed setup attempts in accounting. Record quota baselines before launching when subscription-window comparisons are a goal.
- Verify special boss mechanics before ending turns; use evidence rather than worker confidence to explain deaths.
- Preserve existing saves on interruption. Do not silently switch models, discard runs or publish private traces.

A complete-run success rate, controlled same-seed comparisons, a verified cache fix and reliable per-run subscription attribution remain open work.
