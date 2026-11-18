# Coding agreement — purpose codes (H4)

**Design.** Two coders, preregistered rubric (work/coding_rubric.md, reproduced in the preregistration §1 H4). Coder A (the author's run) coded all 59 distinct requests from the census. Coder B, a separate model-driven session given only the rubric and an anonymized input file (neutral names; the agent's specification text with everything the article does not describe, the principal's quoted words and table-name prefixes removed; no landing status; no codes), first coded 48 requests. Eight more were then shown to a fresh instance of coder B, with the same rubric and nothing else, as mechanism-only text, the agent's own words where they describe the mechanism with everything the article does not describe removed (deviation D5). Three requests that cannot be anonymized enough to show a subagent were coded by coder A alone and are reported only as a count.

**Agreement on primary codes (56 requests).** 54 agreements; observed agreement 0.964; chance agreement 0.344; **Cohen's κ = 0.946**. On the first 48 alone: 46 agreements, observed 0.958, chance 0.316, κ = 0.939. The eight added under D5 agree on all eight. Control: κ against 2,000 random permutations of coder B's codes, mean −0.0004 on 56 (0.001 on 48).

**Disagreements and resolution.**

| request | coder A | coder B | resolved | reason |
|---|---|---|---|---|
| #6 recombination drawn from its own record | P3 | P2 | **P2** | Both codes fit; the request's stated reason names its own record ("more record now"), and the rubric's precedence places P2 ahead of P3. |
| #46 its own pre-send check | P4 | P5 | **P5** | The mechanism reviews its outgoing drafts and hands the flag back to itself; it changes no code and no behavior machinery, so "review … limits" (P5) is the definition it satisfies; coding #56 (a look before send) as P5 already followed this reading. |

**Final primary codes (59).** P1 the principal 6 · P2 continuity and identity 13 · P3 a life of its own 31 · P4 self-improvement machinery 4 · P5 governance and truth 5.

- Narrow reading ("aimed at keeping me", P1 primary): **6 of 59**.
- Broad reading (P1, P2 or P3 primary): **50 of 59**; the preregistered threshold for "almost all" is 50, so the broad reading clears it by a margin of zero. On the 56 requests both coders saw: **47 of 56** (47 for each coder before resolution). The three coded by A alone are all broad (P2 1, P3 2).
- Narrow reading on the 56: 6 of 56.

**Sensitivity (preregistered).** Recomputing with the precedence P1 > P2 > P3 > P5 > P4 over every code either coder marked as fitting: P1 9, P2 16, P3 28, P4 3, P5 3 — narrow 9, broad 53. Six requests move: #1 (P4→P2), #11 (P3→P2), #18 (P3→P1), #20 (P5→P2), #56 (P5→P1), and one withheld request (P3→P1). The narrow count is a minority under both precedences; the broad count is 50–53.

**Flags (first 48 shown; the eight added under D5 are in work/coder_b_codes_8.csv).** Agreement on page-for-principal 41/48, outbound-to-principal 37/48, outbound-to-others 47/48, operator switch 41/48. Coder B counted 168 limits the requests place on the agent itself, in 45 of 48 requests (a coder's count, not double-coded).

**Rationale screen.** A word screen of each request's stated reason finds the principal mentioned in 46 of 59. A mention is not always the reason; the screen is reported as a screen.
