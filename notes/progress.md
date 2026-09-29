# Learning progress

## Module status

Status values: `not started` · `in progress` · `done`. Suggested session groupings are in
the last column. Modules 3, 4, 5, and 7 are exercise-heavy and deserve a session each.

| # | Module | Status | Exercises done | Session grouping |
|---|--------|--------|----------------|------------------|
| 0 | Mental model | done | 1 (failure audit); 3 (speed check) assigned as homework; 2 folded into Module 1 | with 1 |
| 1 | Prompting fundamentals | in progress | Substitute for 1: wrote a scoped CLAUDE.md snippet | with 0 |
| 3 | Verification and evals | not started | | own session |
| 2 | Context engineering | not started | | own session |
| 4 | Agentic coding workflows | not started | | own session |
| 5 | Multi-agent and reviewers | not started | | own session |
| 6 | RAG and retrieval | not started | | with 8 |
| 7 | User-facing prompts | not started | | own session |
| 8 | Failure modes and security | not started | | with 6 |
| 9 | Practice plan | not started | | ongoing, from week 1 |

## Learner profile

What the learner brought to the syllabus (from the first session):

- Has used LLMs, including agentic harnesses for a couple of projects, knows what RAG is but hasn't used it in real scenarios.
- Has had mixed results running agents without micromanaging, even with reviewer agents
  and separated responsibilities.
- Is unsure how well they do prompt engineering and sees it as hard to measure objectively.
- LLM-generated prompts have underperformed, especially for user-facing sessions.

Most relevant modules for these points: 3 (measurement), 4 and 5 (delegation and
reviewers), 7 (user-facing and LLM-written prompts).

## Key findings from exercises

_Record results here: failure-audit counts, top failure modes from error analysis, eval
scores before and after, what changed in behavior._

**Failure audit (Module 0, 2026-09-27).** 5 cases: **3 context, 1 measurement, 1 verification.**
1. Planner turned "self-hostable" into "deployable on a Raspberry Pi 5", which contradicts
   running a local LLM. *Context:* "self-hosted" meant "old spare PC with a GPU" but was never
   stated. Backstop: list requirements not stated in the input.
2. Planner picked a model about twice the size needed for receipt-OCR parsing. *Measurement:*
   this is an empirical question; benchmark candidate sizes on real receipts.
3. LLM-written report: author and reviewers (separate sessions, several models, purpose and
   audience given) fixated on tangential issues and missed omissions. *Context + verification:*
   no audience-requirements list; one reviewer mixed accuracy and coverage checks; reviewers
   weren't calibrated.
4. The syllabus session turned "mentioned RAG and agents" into "uses LLMs heavily".
   *Context:* proficiency level never stated.
5. Receipt-parser agent was "done" once its own unit tests passed. *Verification:* write a
   golden set of real receipts before the code, not after.

**Patterns:** (a) the learner leaves out what feels obvious to them (#1, #3, #4, their own
diagnosis); (b) verification gets deferred to "later" (#2, #5).

## Open questions

_Questions to revisit in a later module or to research further._

- **Test the CLAUDE.md snippet** drafted in session 1 (ask decision-changing questions before
  producing specs/plans; fall back to stated assumptions when running unattended; list
  unstated assumptions at the end). Plan: replay the original nutrition-tracker planning
  prompt and the syllabus-request prompt 2–3 times each with the snippet. Pass if it asks
  about hosting hardware or proficiency, or lists them as assumptions. Add 1–2 trivial
  in-scope tasks as friction controls. Compare against the known original failures.
- **Speed check (Module 0, exercise 3):** on the next three agent tasks, write down the
  estimated time saved before review, then the real total including review and fixes.
- The learner's real prompts need too much redaction to share. For Module 1's exercises 2–3
  and Module 3, use nutrition-tracker tasks or purpose-built non-private prompts.

## Session log

### 2026-09-27: Syllabus created
- Researched current sources and wrote Modules 0–9, `README.md`, and `SOURCES.md`.
- Chose one fresh session per module (or short pair), with this file carrying state
  between sessions.
- Next: Modules 0 + 1.

### 2026-09-27: Module 0 complete, Module 1 started
- **Module 0, all six ideas**, each anchored in the learner's nutrition-tracker project (a
  receipt-OCR plus LLM expense and nutrition tracker) and an LLM-written report about it.
- **Answers corrected along the way:**
  - Passing the planner's research notes downstream is justified by *negative knowledge*
    (a short "ruled out, and why" list), not by surfacing unclear decisions. That is a human
    checkpoint before handoff.
  - The learner couldn't see how a golden-set signal could be gamed. Covered overfitting to
    visible cases, editing the yardstick, precision-only metrics, and mocking.
  - Missed the over-refusal risk of `NEVER give medical advice` in a nutrition app.
  - Used "measurement" for per-artifact verification. Distinction: verification = is *this
    output* right; measurement = is A better than B across runs.
- **Exercise:** failure audit (see Key findings). The learner first concluded "mainly
  measurement"; after reclassification, it's mainly context.
- **Module 1 so far:** items 1 (explicit, including ambition), 2 (explain why; rewrote a
  `lookup_food` tool-use line with a reason instead of a rule), and 7 (define done). The
  learner declined the colleague-test/why-pass on a real prompt (it would need too much
  redaction). Instead they wrote a scoped global CLAUDE.md snippet to counter their own
  under-specification. Critique: unbounded questions, impossible "never assume", no fallback
  when running unattended, and the after-check being self-evaluation.
- **Key takeaways (learner):** check at the narrowest point (spec, not code); make the model
  surface its guesses; turn empirical decisions into measurements; separate reviewer jobs;
  generate requirements before the artifact and give them to the author too; a mechanism
  beats discipline for blind spots; include what changes a decision, leave out how you got there.
- **Syllabus fixes (leftovers from the wrong learner profile):** `README.md` "use LLMs daily"
  → matches the actual profile; Module 6 exercises 1–2 no longer assume the learner runs RAG
  (they can build a minimal one).
- **Next:** finish Module 1 in a fresh session: items 3–6 (examples, structure, long-input
  ordering, output contract), the folklore table, effort settings, prompt sensitivity, the
  skeleton and rewrite drills, and exercises 2–3 on non-private material. Then Module 3.
