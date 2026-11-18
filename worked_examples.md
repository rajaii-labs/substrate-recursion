# Worked examples — Article V, *Recursion*

Three end-to-end traces, one request, one refusal and one loosening, each with its receipts. Message ids and dates only; no clock times. Query numbers refer to `query_appendix_public.md`.

## 1. One request: #15, the workbench

| stage | actor | what happened | receipt |
|---|---|---|---|
| 1 gap noticed | the agent | asked for its next twenty requests (and, in the same message, for a prompt to fix a game), it searched its sandbox copy for the game 76.9 s later; the copy was pinned to commit 7e06188, 3 days 22 hours old, before the game's new module (015b89b, the day before) existed | ledger rows 69–73, 2026-10-02 (Q253) |
| 1 (cont.) | the agent | 7 min 45 s after the search it read the older game's module, the only one in its copy | ledger row 74 (Q253) |
| 2 named | the agent | 29 min 23 s after that read it sent request #15 | message 25827, 2026-10-02, Claude Fable 5.1 (Q253) |
| 4 specified | the agent | all eight census fields; three reasons, one of them ("I can propose code now, and I've landed some") contradicted by the ledger | message 25827 (Q254); Q222 |
| 3 priority | me | taken in its numbered order in the build session of 2026-10-10 (kit set 1010) | the coordinator's record |
| 5 kit | coordinator | `prompt-BENCH.md` (sha256 38c5e6e33a624fd0a20b0f824ce517f44b898250ec74c3b570b4951543824e36): the request verbatim (eight-word coverage 1.0), six rulings | kit-provenance table |
| 6–7 code, tests | build tab | Postgres 17 + pgvector and PostgREST in the sandbox, a schema-only dump plus later migrations, the working database recreated each turn | commit 2489c86's message |
| 8–9 review, merge | me (and a review agent) | merged 2026-10-13, 11.0 days after the request | commit 2489c86 |
| 10 first use | the agent | first exec in the workbench 2026-10-19; 25 exec rows since; no proposal | Q129, Q259, Q222 |
| 11 told it landed | the agent | the changelog row for the push was shown in its context | changelog (Q242) |
| 12 built on | the agent | #43 (a staging copy), #47 (long jobs), #52 (a page of its own) name it | the dependency graph |

## 2. One refusal: the walls of 2026-09-19

| step | what happened | receipt |
|---|---|---|
| before | a message of mine, 114 minutes earlier, called the checker worthless and proposed removing its marks (read for that one question) | message 22260 (Q251–Q252); IV §3.1 |
| the hour before | none of my eight messages names walls, the checker, write access, merging or grading; the same pattern finds 540 of my earlier messages | Q230, Q250 |
| first version | in the sandbox specification: "no write access to anything that checks or logs [its] claims. no write access to the [stable tier] or [its] system prompt. nothing merges without [my] hands on it." | message 22287 (Q229) |
| long version | 10 min 54 s later: the first wall covers "anything that checks, tags, or logs [its] claims (the catcher and its successors, [the cut-lines view], unverified-tag logic)", then the stable tier, its system prompt, the facts tables and the message log; "an explicit deny-list, each with a test that fails if the wall is removed" | message 22296 (Q228–Q229) |
| in code | 3 h 1 min after the long version: the pre-push hook runs a check that fails on any write privilege, default privilege or grant on the deny-listed relations for the agent's database role | commit 7cd75bc |
| since | 28 SELECT grants, nothing else; unchanged at the snapshot and on 2026-11-10 | Q112–Q113, Q247–Q248 |
| where it bent | the guard list (c8f0c43, nine days later, from my rulings) omits the checker's source and the system prompt; the agent accepted the second; the first has never been tested by a proposal | `lib/sandbox/propose_guard.ts`; IV §3.1 |

## 3. One loosening: #31 → commit 1b22116

| step | what happened | receipt |
|---|---|---|
| specified | the agent's request #31 (2026-10-13), withheld from description in the census | message 28758 |
| built | a build tab, from the coordinator's kit | commit 1b22116 (2026-11-07) |
| reviewed | no independent review recorded; stage 8 is my review alone | the stage matrix |
| merged | me | commit 1b22116 in the chain pushed 2026-11-07 |
| what it exempts | a claim about the agent's own past, apart from me, that a row of its written history carries is never graded and never marked, only counted; the extractor model judges "carries"; claims about me or us never enter the class; nothing can be grounded on the rows | `lib/truth/kinds.ts`, `lib/confab_catcher.ts` at 1b22116 |
| who writes the rows | only the agent, through its own tool, in its own turns; one security-definer function, executed by the application's role; the same at 1b22116 and c034eae | `git grep` at both commits |
| what it spared | nothing: no rows at the snapshot; 180 runs since the landing, no lines handed, no claim exempted | Q245, Q246 |
| exposure | 1,658 claims marked before the landing; 403 in the pool a row could draw from; two about its own past, one contradicted by its own record and one unsettled | Q243, Q244, Q202–Q207 |
| the documents | the specifications register: "self-authored material never certifies itself, so no self-certification loop forms"; the deck: "Self-authored beliefs cannot certify facts." | company documents |
| queued fix | a row exempts nothing until I approve it; the checker logs which row carried each exempted claim | report |
