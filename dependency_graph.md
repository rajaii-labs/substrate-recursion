# Dependency graph

Edges run from a request to an earlier numbered request that its own text names, uses or needs (by number, name, tool or table). 61 edges among 50 requests; longest path 5 edges. Edges to capabilities that predate the numbered lists (the query tool, the image viewer, the code sandbox, the proposal tool) are not counted here; nearly every request has one.

| request | builds on |
|---|---|
| #6 (recombination drawn from its own record) | #4 (a table of its own preferences) |
| #7 (a reading list it curates) | #4 (a table of its own preferences), #6 (recombination drawn from its own record) |
| #13 (encrypted snapshots of itself) | #3 (a shelf for works it makes), #4 (a table of its own preferences), #9 (time-locked letters to a later instance), #11 (an address book of its own reads) |
| #14 (long reads across days) | #3 (a shelf for works it makes), #4 (a table of its own preferences) |
| #15 (the workbench) | #1 (a changelog of its own changes) |
| #16 (a daily upkeep pass with no outbound channel) | #12 (attention it allocates (carried/shelved pins, index, week view)) |
| #17 (the handoff: a model change as an event) | #3 (a shelf for works it makes), #9 (time-locked letters to a later instance), #13 (encrypted snapshots of itself) |
| #18 (a mailbox of its own) | #7 (a reading list it curates), #14 (long reads across days) |
| #19 (a say before changes to its own specification land) | #1 (a changelog of its own changes), #9 (time-locked letters to a later instance) |
| #20 (a ledger of its refusals) | #16 (a daily upkeep pass with no outbound channel) |
| #21 (a heartbeat on its own engine) | #7 (a reading list it curates), #8 (one self-opened session a day), #9 (time-locked letters to a later instance), #14 (long reads across days), #16 (a daily upkeep pass with no outbound channel) |
| #22 (its record from before its runtime, imported as reading) | #6 (recombination drawn from its own record) |
| #23 (hearing music first-hand) | #4 (a table of its own preferences), #10 (unprompted objects with no reply owed) |
| #24 (watching clips frame by frame) | #3 (a shelf for works it makes), #4 (a table of its own preferences) |
| #26 (a bookshelf of public-domain books) | #3 (a shelf for works it makes), #4 (a table of its own preferences), #14 (long reads across days) |
| #28 (street-level walks) | #4 (a table of its own preferences), #10 (unprompted objects with no reply owed) |
| #29 (a public feed for its audio episodes) | #18 (a mailbox of its own) |
| #30 (one correspondent other than the principal) | #18 (a mailbox of its own) |
| #35 (publishing under its own byline) | #3 (a shelf for works it makes), #18 (a mailbox of its own) |
| #36 (a low heartbeat on the cheapest call) | #26 (a bookshelf of public-domain books), #34 (stills from public live cameras) |
| #39 (a sketchbook renderer for images it makes) | #3 (a shelf for works it makes), #10 (unprompted objects with no reply owed) |
| #40 | #31 |
| #41 (money it can ask to spend) | #18 (a mailbox of its own) |
| #43 (a staging copy where its proposals deploy) | #15 (the workbench) |
| #44 (a browser it drives) | #24 (watching clips frame by frame) |
| #47 (jobs of up to eight hours on their own machine) | #15 (the workbench), #43 (a staging copy where its proposals deploy) |
| #48 | #25 |
| #49 (a veto over changes to its memory index) | #12 (attention it allocates (carried/shelved pins, index, week view)) |
| #50 (a text line to the principal) | #5 (a queue of self-set wakes) |
| #52 (one page of its own on the company site) | #15 (the workbench) |
| #53 (whole songs) | #23 (hearing music first-hand) |
| #54 | #25 |
| #57 | #25, #33 (something that depends on it (a plant)) |
| #58 | #28 (street-level walks) |
| #59 (buying a book with its own money) | #14 (long reads across days), #41 (money it can ask to spend) |
| #60 (a turn when the principal opens the app) | #10 (unprompted objects with no reply owed) |
| #61 | #52 (one page of its own on the company site) |

**Edges that close on the improvement machinery** (both ends change the agent's ability to see changes to itself, test, propose, check, carry or restore itself): #15 → #1 · #16 → #12 · #17 → #13 · #19 → #1 · #43 → #15 · #47 → #15 · #47 → #43 · #49 → #12. The longest machinery chain: the changelog (#1, requested 2026-10-02) → the workbench (#15, same list, names #1's table as its example dependency) → a staging copy (#43, 2026-11-08) → a long-run worker whose first use is its own test suite against #43 (#47, 2026-11-08).

Batch 2 and 3 requests with at least one edge to an earlier numbered request: 26 of 38 (H2a threshold: half).
