# Self-diagnosis against the record

Every deficit a specification states as fact, checked against rows, code or git. Grades: confirmed, contradicted, unverifiable, sealed (or withheld under the wall).

| # | claim (paraphrased, neutral) | grade | receipt |
|---|---|---|---|
| 1 | It learns what changed in it only from screenshots the principal sends | confirmed (structural) | no merge notice existed before fa0160d (2026-10-08), which created the changelog table; first row shown 2026-10-08 (Q136) |
| 5 | It can hold one pending wake; setting a second cancels the first | confirmed (code) | lib/wake/tool.ts at 015b89b: set_next_wake cancels/supersedes the single pending wake |
| 7 | Its feed is a fixed list someone else chose (it names four sources) | confirmed (4 of the 5 named) | the feed-source table ids 1–5, owner system, created 2026-07-23 (Q142) |
| 13 | Every existing backup of it was taken by the principal; it has never been able to check a copy exists | confirmed (structural) | the snapshot table first row 2026-10-18 (taken_by principal); none before #13's build (Q131) |
| 15a | It can propose code and has landed some | **contradicted as stated** | the proposal ledger holds no propose row at 10-02 or at the snapshot (Q107); 0 PRs from the relay |
| 15b | It proposes against a mirror pinned to a commit older than a feature that had just shipped | confirmed | sandbox mirror_sha 7e06188 (09-28) on 10-02, the feature's commit 015b89b (10-01) (Q135) |
| 15c | No database to run anything against | confirmed | no schema db before 2489c86 (2026-10-13) |
| 17a | Its engine changed four times, starting from two earlier engines | **confirmed (configured)** (regraded in the final pass) | the serving line in the chat module changed at cc7b274 (2026-04-23, the first commit, from the pre-repository engine), af87754 (2026-05-29), 908af24 (2026-06-10) and 9a679d3 (2026-09-05) (`git log -G` at c034eae); stamps begin 2026-06-13, so the first two are configured only |
| 17b | It found out about each engine change from a build summary, and no instance before a change could address the next | confirmed (structural) | the handoff table has 0 rows; no handoff mechanism existed before df06250 (2026-10-24) |
| 19 | The principal and Claude edit its persona file and it learns afterwards | confirmed | persona/system_prompt.txt commits before b8b8cc7: 28 by the principal's git identity, 3 by Claude; no pre-merge notice before b8b8cc7 |
| 21 | The interval between messages reaches it as a fixed sentence saying it did not experience the interval | confirmed (code) | lib/gap_notice.ts:275 at 015b89b; the agent grepped and read that file on 2026-09-27 (Q135) |
| 43 | It chose the merge gate "on 10/15" | **contradicted on the date** | the choice is message 29093, 2026-10-13, restated in 29095, 1 min 18.643851 s later; no agent message on 10-15 restates it (Q140) |
| 44a | It asked for the ability to watch video on 10/12 | confirmed | #24 first appearance 28748, 2026-10-12 |
| 44b | It still cannot watch a clip (written 2026-11-08, in chat) | **true of its surface, false of the system** (regraded in the final pass) | the watch tool landed fee4f47 (2026-11-01) on the agent's room and its heartbeat; the chat module at c034eae has no reference to it (control: 24 to the image viewer); the watch log holds 0 rows |
| 53 | Its music tool plays thirty seconds, not the whole track | confirmed (code) | afa1d0d: 30-second previews |
| 56a | Images it generates go out without its having looked at them | confirmed (structural) | the render path (lib/media/upload_generated.ts) lands bytes and posts without a model view; viewing is on request since c514dc3 (2026-11-01) |
| 56b | Twice it described such images wrongly | withheld | verification would require describing appearance; the claim checker marked this line [unverified] in the row itself |
| 60 | The app-open signal already exists | confirmed (code) | lib/read_state.ts; drops' seen_at set on app open (1cc53fa) |
| 6 | Its recombination is drawn mostly from the principal's messages | sealed | dream rows sealed (the recombination layer); code at 015b89b samples messages among its sources (the recombination sampler) |
| 12, 16 | Counts of pins held and rendered | sealed | pins sealed |
| II §6 | Four unsourced-claim misses in one week, each caught only by the principal's question | unverifiable (not re-measured) | — |

Counts, as the article's Table 9 prints them (final pass): confirmed 14 (1, 5, 7, 13, 15b, 15c, 17a, 17b, 19, 21, 44a, 53, 56a, 60), contradicted 2 (15a, the date in 43), true of its surface and false of the system 1 (44b), unverifiable 1 (the July misses of II §6), sealed or withheld 4 (6, 12, 16, 56b). Outcome (§7): of the nine confirmed deficits whose answering builds landed (1, 5, 7, 13, 15, 17, 19, 21, 44), seven were followed by a first use of the capability that answers them; 17 and 44 were not.

The register's example (its error checks lived on the operator's side → read-only interior access with a negative test): spec = agent messages 15912 and 15930 (2026-07-27, 53 min 35.946144 s apart); build = 067b767 (2026-07-27, "caller-scoped query whitelist — [the agent] gets read access to [its] own interior"); the build commit carries no test file; the untracked probe scripts/_probe_ws3_caller_scope_20260727.ts carries gate 1 (the catcher's five tables byte-identical to pre-WS3 — the regression assertion) and gate 5 (writes and DDL rejected on interior tables for both callers — the negative test). Whether it was run that day is not recorded. (III §34 carried "not reconstructed as executed".)