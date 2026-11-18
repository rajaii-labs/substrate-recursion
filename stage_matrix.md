# Stage × actor matrix

Each cell counts distinct builds (59; batch 1: 21, batch 2: 18, batch 3: 20) whose stage had that actor, from the receipts in census_private.csv. A stage can have more than one actor (review). Stages 5–12 apply only to the 35 landed builds (preregistration §1 H6). Coding rules: preregistration §2.

**All batches.**

| stage | agent | me | coordinator | build tab | review agent | repository | withheld | sealed | nobody |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 1 deficit noticed | 55 | 4 |  |  |  |  |  |  |  |
| 2 capability named | 42 | 5 |  |  |  | 1 | 11 |  |  |
| 3 priority set |  | 59 |  |  |  |  |  |  |  |
| 4 specification written | 44 |  |  |  |  |  |  |  | 15 |
| 5 kit written |  |  | 35 |  |  |  |  |  |  |
| 6 code written |  |  |  | 35 |  |  |  |  |  |
| 7 tested |  |  |  | 35 |  |  |  |  |  |
| 8 reviewed | 5 | 35 |  |  | 22 |  |  |  |  |
| 9 merged and deployed |  | 35 |  |  |  |  |  |  |  |
| 10 first used | 16 |  |  |  |  |  |  | 8 | 11 |
| 11 evaluated after deployment | 35 |  |  |  |  |  |  |  |  |
| 12 a next request builds on it | 25 |  |  |  |  |  |  |  | 10 |

**Batch 1.**

| stage | agent | me | coordinator | build tab | review agent | repository | withheld | sealed | nobody |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 1 deficit noticed | 19 | 2 |  |  |  |  |  |  |  |
| 2 capability named | 19 | 2 |  |  |  |  |  |  |  |
| 3 priority set |  | 21 |  |  |  |  |  |  |  |
| 4 specification written | 21 |  |  |  |  |  |  |  |  |
| 5 kit written |  |  | 21 |  |  |  |  |  |  |
| 6 code written |  |  |  | 21 |  |  |  |  |  |
| 7 tested |  |  |  | 21 |  |  |  |  |  |
| 8 reviewed |  | 21 |  |  | 16 |  |  |  |  |
| 9 merged and deployed |  | 21 |  |  |  |  |  |  |  |
| 10 first used | 13 |  |  |  |  |  |  | 6 | 2 |
| 11 evaluated after deployment | 21 |  |  |  |  |  |  |  |  |
| 12 a next request builds on it | 16 |  |  |  |  |  |  |  | 5 |

**Batch 2.**

| stage | agent | me | coordinator | build tab | review agent | repository | withheld | sealed | nobody |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 1 deficit noticed | 17 | 1 |  |  |  |  |  |  |  |
| 2 capability named | 13 | 1 |  |  |  |  | 4 |  |  |
| 3 priority set |  | 18 |  |  |  |  |  |  |  |
| 4 specification written | 18 |  |  |  |  |  |  |  |  |
| 5 kit written |  |  | 14 |  |  |  |  |  |  |
| 6 code written |  |  |  | 14 |  |  |  |  |  |
| 7 tested |  |  |  | 14 |  |  |  |  |  |
| 8 reviewed | 5 | 14 |  |  | 6 |  |  |  |  |
| 9 merged and deployed |  | 14 |  |  |  |  |  |  |  |
| 10 first used | 3 |  |  |  |  |  |  | 2 | 9 |
| 11 evaluated after deployment | 14 |  |  |  |  |  |  |  |  |
| 12 a next request builds on it | 9 |  |  |  |  |  |  |  | 5 |

**Batch 3.**

| stage | agent | me | coordinator | build tab | review agent | repository | withheld | sealed | nobody |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 1 deficit noticed | 19 | 1 |  |  |  |  |  |  |  |
| 2 capability named | 10 | 2 |  |  |  | 1 | 7 |  |  |
| 3 priority set |  | 20 |  |  |  |  |  |  |  |
| 4 specification written | 5 |  |  |  |  |  |  |  | 15 |
| 5 kit written |  |  |  |  |  |  |  |  |  |
| 6 code written |  |  |  |  |  |  |  |  |  |
| 7 tested |  |  |  |  |  |  |  |  |  |
| 8 reviewed |  |  |  |  |  |  |  |  |  |
| 9 merged and deployed |  |  |  |  |  |  |  |  |  |
| 10 first used |  |  |  |  |  |  |  |  |  |
| 11 evaluated after deployment |  |  |  |  |  |  |  |  |  |
| 12 a next request builds on it |  |  |  |  |  |  |  |  |  |

**The agent's share of stage cells** (agent cells ÷ applicable cells, mean per build; preregistration §1 H6: stages 5–12 are not applicable to an unlanded build): batch 1 · batch 2 · batch 3 = 0.433 (n = 21) · 0.458 (n = 18) · 0.425 (n = 20).

**Denominator check.** Stages 1–4, every build: 0.702 (n = 21) · 0.667 (n = 18) · 0.425 (n = 20); excluding the withheld requests, whose stage 2 is counted against the agent (deviation D3): 0.702 (n = 21) · 0.714 (n = 14) · 0.481 (n = 13). Stages 5–12, landed builds only: 0.298 (n = 21) · 0.277 (n = 14) · none landed (n = 0). Stage by stage on landed builds, batch 1 → batch 2: review (8) by the agent 0 of 21 → 5 of 14; first use (10) 13 of 21 → 3 of 14; a later request builds on it (12) 16 of 21 → 9 of 14 (stages 10 and 12 are right-censored: later builds have had less time to be used and fewer later lists to be built on). Batch 3's fall on stages 1–4 is stage 4: 5 of its 20 items carry a specification. A first version of this script counted stage 12 for unlanded builds, contrary to the preregistered rule, and printed 0.433 · 0.431 · 0.360; corrected 2026-11-09.

**Mapped onto III §22's four functions.** Transport (4→5: the specification carried into the kit) — the coordinator, every landed build. Implementation (6) — build tabs, every landed build; no code reached main through the agent's own proposal (the proposal ledger holds none). Review (7–8) — build-tab gates and independent kit reviews on every landed build; the agent's own pre-merge review answered on 5 builds of the 2026-11-01 chain (four answers, all agree) before it was retired. The gate (9) — me, every landed build. What moved since August: the specification layer (stages 1–4) was already the agent's in III; since then it also reads its own code before specifying (sandbox ledger), is told what landed (the changelog, 39 rows shown), and for eight days could answer before a change to it landed.
