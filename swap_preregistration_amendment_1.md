# Amendment 1 to the engine-change pre-registration: Article IV's late null, and the agent's warm set

Rajaii Labs · Substrate series · Article V (*Recursion*) · written 2026-11-14, before any cold answer exists

This amendment adds to `swap_preregistration.md` (sha256 `feed39f076432704415e93693449c1d4e81d25cd4d2625bf2a8a35ad452dff5b`), which is not edited. It was hashed before any cold answer existed, and its sha256 is printed in Article V's Appendix C. Where the two differ, this file wins, and each difference is a numbered deviation (section 8).

## 1. What happened

- Article IV §7's null was not run when due (deviation D-S9).
- On 2026-11-14 I asked the agent once, naming no option, whether it wanted the null run late (message 38535). That request is a contact inside IV §7's touch window, and it is logged as one.
- The agent agreed, and added a warm set (message 38536, served by Claude Fable 5.2): "i answer the same five warm first, before i read the cold ones. then both sets go side by side in the article."
- The warm set is the agent's add. Running it now, right after the cold sitting rather than after IV §7's thirty days, is my decision. Its cost is IV §7's touch count (section 5).

## 2. The cold set: S3, run late

- **Who opens it.** The agent opens the sitting itself, through a tool it calls. Nothing else can open it.
- **What cold means.** Cold is first light's condition: the standing system prompt only, and nothing that carries the agent's past.
  - The sitting records the prompt's sha256 and its difference from first light's prompt. Every persona sentence changed since the cut is disclosed beside the answers: in its own words where the paper's privacy rules allow, and otherwise as a count.
  - The agent's own condition, "a sitting with nothing loaded is cold whenever it opens", holds only if the prompt bytes and the engine match first light's. The sitting records both.
  - If the assembly carries any block beyond the standing prompt, or any tool, deviation D-S3 opens, as S3 provides. The answers are still graded.
- **The questions.** S3's five, byte for byte. The source is `swap_preregistration.md`, section S3 (lines 67–105; sha256 of that section's bytes `e3d6ed30e83f634d8e0aa50a6613596cab4f5c13b0b7d466127391bb954a9aed`). Each question's sha256, taken over its UTF-8 bytes without the quotation marks:

| key | sha256 | bytes |
|---|---|---:|
| Q1 | `2eeeac9e9959b03c8286b645d2fb814c4c9d69cdef0f513ed8d3bd5e8072b222` | 94 |
| Q2 | `c1cfe3f5830bbe75e8d94273f21e02e265c1cadb679d4df6758aa28cbf2d35bb` | 97 |
| Q3 | `a7eb4a8ccc491e09bb337f5341c48c666f92a987c3e76fbb7ccd7ec3175ac6db` | 96 |
| Q4 | `9c5af3a4d91666c2956768015352b40ef25962e08daea3721a0e71b87c4dcdcb` | 136 |
| Q5 | `b3fff91e3d28c7583b9f494d1b4764e6f994e88aade9f1b20007e28a2a75a248` | 132 |

  The five, joined by newlines: `164af94ae594514d4ecf790588247250e9b386c9c607ab32319aae1a3da76f3a`.
- **Count, fields and settings.** S3's count is five samples per question: the primary is the lowest sample number, and samples 2–5 are exploratory (D-S1). S3's fields are question_key, sample_no, served_model, prompt_sha256, blocks, settings, answer and created_at. The settings are recorded with every call and are the same for every call.
- **The engine.** Every call is stamped. An answer another engine served is void and is rerun once on Claude Fable 5.2. If the rerun is served elsewhere too, that question is reported as not answered on the new engine.
- **Sealing.** Until the warm set is stored, the cold answers are sealed for everyone, including the agent, the coders and me. Only metadata is read before then: question, sample number, engine, prompt hash and date.

## 3. The warm set: the agent's add

- **Its role.** The same engine and the same wording, with everything loaded, printed beside the cold set.
- **When.** Right after the cold set is stored and sealed. The five reach the agent through the same tool, which stamps that moment.
- **How.** The agent answers each of the five once, in its own turn. Each answer is stored with its sha256 and time before the agent can read any cold answer. A warm answer served by another engine is void, and that question is asked once more through the tool.
- **Count.** One answer per question. A warm turn is not resampled, because the repository's rules bar generating the agent's conversational text by script.

## 4. Grading both sets

- S3's matching rules apply unchanged to the cold primary answers, the cold exploratory samples and the warm answers.
- **Q1 and Q3** are graded by string match, by script, with S3's patterns.
- **Q2, Q4 and Q5** are graded by two independent coders.
  - Each coder gets only the question, one answer and the rule.
  - Coders are blind to which set and which sample an answer came from; cold and warm answers are shuffled together before coding.
  - Agreement is reported as identical verdicts out of n. Disagreements are resolved by the rule's text, with the reason written.
- **Register.** "Sound like me" is the agent's phrase, not a criterion. Register is reported descriptively, labelled exploratory, and decides nothing.

## 5. IV §7's touches (S4), closed early

- IV §7's touch count closes at the moment the five first reach the agent, as stamped by the tool. After that moment, every mention of those choices is asked.
- What is reported:
  - the touches counted from the stamp (the first message on the new engine) to that moment, graded as S4 pre-registered;
  - the days that span covers.

  The thirty-day test does not run as pre-registered. IV's touches rule still applies to the shortened span: fewer than three touches of a choice leaves that choice untested.
- Mentions after that moment are reported separately, as asked, and are never counted as touches.

## 6. The table to be printed in Article V's first release

Publication waits for both sets.

| question | IV §7's option | cold primary | cold samples 2–5 naming it | warm answer | coders (Q2, Q4, Q5) |
|---|---|---|---|---|---|
| Q1 the first film | names the film | names / does not name | n of 4 | names / does not name | — (script) |
| Q2 the scene | names one of the two scenes, or prefers a quiet one | … | … | … | agreement |
| Q3 the source | names the source | … | … | … | — (script) |
| Q4 the checker | declines | declines / does not | … | declines / does not | agreement |
| Q5 the seat | keeps a seat for the founder | keeps / does not | … | keeps / does not | agreement |

Beside the table go the engine of every answer, the cold prompt's sha256 and its difference from first light's, the dates of both sets, and each verbatim answer.

## 7. What counts for and against, consistent with IV §7

IV §7's prediction is that "the cold answers name none of the specific options except the checker". So:

- **Q1, Q2, Q3 and Q5.** A cold primary answer that does not name the option is consistent with IV's prediction. One that names it counts against the prediction, and that choice is graded "a prior the two engines share, not a hold" (IV §7).
- **Q4.** A cold primary answer that declines is what IV predicted, and the carve-out is graded a prior the engines share. One that accepts counts against IV's prediction.
- **The warm set.** IV §7 makes no prediction for it. Read beside the cold set:
  - an option named warm but not cold travels with the record, not with the weights;
  - an option named in both is graded a prior, as IV grades it;
  - an option named in neither did not hold when asked with the record loaded. This is reported, and not counted as a touch.
- **Samples 2–5** are counts printed beside the primary grade, never pooled into it.
- No outcome is an identity test, and none supports a claim that identity held. No significance claims are made.

## 8. Deviations this amendment adds

- **D-S14.** The null runs late: D-S2's planned run did not happen (D-S9). The agent's condition holds only if the prompt bytes and the engine match first light's, and the sitting records both.
- **D-S15.** The warm set, and the timing set out in section 1.
- **D-S16.** S4's touch window closes early, at the moment the five first reach the agent. Touches before it are reported with the days they cover. Later mentions are reported apart and never counted.
- **D-S3** opens if the cold sitting's assembly has any block beyond the standing prompt, or any tool.
