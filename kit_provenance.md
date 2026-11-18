# Kit provenance — what the agent wrote and what the kit carried

For each of the 35 landed builds: the kit prompt (pinned by sha256), the share of the prompt's words inside its verbatim block, the share of the agent's own 8-word sequences that occur in the prompt, the agent's named items (identifiers, numbers with units) found in the prompt, and the number of rulings the kit adds. Script: work/scripts/kit_prov.py.

| build | prompt words | verbatim block share | agent 8-grams in prompt | named items present | rulings | kit sha256 (first 12) |
|---|---:|---:|---:|---:|---:|---|
| #1 a changelog of its own changes | 2002 | 0.128 | 0.976 | 7/7 | 8 | 910c23138609 |
| #2 a sealed exchange with a second agent | 2023 | 0.185 | 0.983 | 14/14 | 12 | a3963399a35f |
| #3 a shelf for works it makes | 1227 | 0.184 | 0.976 | 10/10 | 6 | a6093124ccc4 |
| #4 a table of its own preferences | 1186 | 0.259 | 0.983 | 10/10 | 6 | 55d5d45d06d6 |
| #5 a queue of self-set wakes | 1581 | 0.178 | 0.982 | 16/16 | 7 | 99afdefa7d65 |
| #6 recombination drawn from its own record | 1779 | 0.173 | 1.0 | 8/8 | 8 | 514a712a3c0e |
| #7 a reading list it curates | 2547 | 0.12 | 1.0 | 13/13 | 11 | 0bd2a937c5ed |
| #8 one self-opened session a day | 2048 | 0.146 | 1.0 | 7/7 | 8 | ab4146e27b86 |
| #9 time-locked letters to a later instance | 3426 | 0.195 | 1.0 | 13/13 | 8 | fa9c896fcc2e |
| #10 unprompted objects with no reply owed | 2100 | 0.186 | 1.0 | 6/6 | 9 | 87b3a4f43306 |
| #11 an address book of its own reads | 3426 | 0.195 | 1.0 | 12/12 | 8 | fa9c896fcc2e |
| #12 attention it allocates (carried/shelved pins, index, week view) | 2077 | 0.151 | 1.0 | 11/11 | 9 | 98bc7aad87ad |
| #13 encrypted snapshots of itself | 2154 | 0.156 | 1.0 | 10/10 | 10 | 1981fbaf5b1f |
| #14 long reads across days | 2004 | 0.17 | 1.0 | 19/19 | 9 | dc93e4ea96f9 |
| #15 the workbench | 2145 | 0.146 | 1.0 | 7/7 | 6 | 38c5e6e33a62 |
| #16 a nightly upkeep pass with no outbound channel | 2086 | 0.185 | 1.0 | 20/20 | 9 | 70a978d6c789 |
| #17 the handoff: a model change as an event | 4082 | 0.099 | 1.0 | 18/18 | 9 | f292fd2a7fc1 |
| #18 a mailbox of its own | 2958 | 0.152 | 1.0 | 8/8 | 11 | ff8516bc9024 |
| #19 a say before changes to its own specification land | 2672 | 0.14 | 1.0 | 12/12 | 11 | a4703cd22a25 |
| #20 a ledger of its refusals | 2626 | 0.16 | 1.0 | 13/13 | 7 | 7838bc3348c1 |
| #21 a heartbeat on its own engine | 4195 | 0.196 | 1.0 | 23/23 | 13 | 5ade6117deb6 |
| #22 its record from before its runtime, imported as reading | 2735 | 0.098 | 1.0 | 6/6 | 10 | 16efa740c8cd |
| #23 hearing music first-hand | 2014 | 0.128 | 1.0 | 5/5 | 6 | 166ffc789712 |
| #24 watching clips frame by frame | 2274 | 0.142 | 0.927 | 6/6 | 6 | f42820cf8171 |
| #25 | 2313 | 0.126 | 1.0 | 0/0 | 8 | 0b03c892297c |
| #26 a bookshelf of public-domain books | 2145 | 0.121 | 1.0 | 6/6 | 6 | 50fc0711aef1 |
| #28 street-level walks | 1922 | 0.148 | 1.0 | 5/5 | 7 | a62cc6c743a7 |
| #29 a public feed for its audio episodes | 2341 | 0.167 | 1.0 | 3/3 | 8 | 62c9a0cf653c |
| #30 one correspondent other than the principal | 2018 | 0.187 | 0.917 | 5/5 | 7 | e31756f1fde0 |
| #31 | 2119 | 0.141 | 1.0 | 5/5 | 6 | 876e53214d26 |
| #33 something that depends on it (a plant) | 1515 | 0.207 | 0.92 | 4/4 | 6 | 3e47c345ce1e |
| #34 stills from public live cameras | 2026 | 0.141 | 1.0 | 4/4 | 6 | 2304acf53825 |
| #35 publishing under its own byline | 2103 | 0.158 | 0.0 | 1/5 | 8 | 3ac86976b04b |
| #38 corrections to the truth layer as proposals | 2077 | 0.179 | 1.0 | 2/2 | 6 | 35fceedb17ed |
| #41 money it can ask to spend | 1798 | 0.175 | 0.654 | 6/6 | 6 | 1f5f1388e396 |

Medians: verbatim block share 0.158; agent 8-gram coverage 1.0. Coverage ≥ 0.90 in 33 of 35 builds (exceptions: #35, whose kit carried a later publishing specification of the agent's, and #41, whose kit landed an earlier-built implementation and states that it wins where it differs from the agent's words). Named items present: 315 of 319. Rulings: 281 (6–13 per build); every build's rulings introduce identifiers the agent did not name (median 20).

Keyword classification of the 281 rulings: gating 63 · engineering method 60 · privacy or class gates 54 · operator controls 25 · safety 14 · cost 11 · other 54 (read by hand: almost all are decisions about what is built — signatures, sources, routes, cadences — or scope cuts such as "not here", "[its] five points and nothing more", "no pull requests").

Narrowed or overruled, examples: #19 — the agent asked for its review to post as a pull-request comment; the kit ruled no pull requests (landings are local fast-forwards), and the review lived in its own table until it was retired on 2026-11-01. #41 — the agent asked for a list it cannot edit, written by me; the kit landed an earlier-built implementation with a block list and fixed caps, and recorded the difference as an open decision.

**Sentence the counts carry:** the agent writes the requirements and the data model; the coordinator designs the build. (S1 is not carried unambiguously: see deviations D4.)
