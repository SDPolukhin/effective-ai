# Module 3: Verification and evals, making "good" measurable

## Why it matters

You said prompt engineering isn't something that can be objectively measured. That's the
most common belief among LLM users, and it's the one this module tries to change. You can't
measure prompts in the abstract, but you **can** measure them against **your task, your
inputs, and your definition of success**. Once you can, prompt engineering stops being taste
and becomes iteration.

This module has two halves:
- **A. Verification**: signals *the agent* uses during a task to know it's done.
- **B. Evals**: signals *you* use across many runs to know whether a prompt, model, or
  workflow change helped.

---

## Part A: Verification inside the loop

### Core idea: give the agent a check it can run

From Anthropic's Claude Code guide:

> *"Claude stops when the work looks done. Without a check it can run, 'looks done' is the
> only signal available, and you become the verification loop."*

A check is anything that returns a signal the agent can read: tests, a build exit code, a
type checker, a linter, a script that diffs output against a fixture, or a screenshot
compared to a design. **This is the single biggest determinant of how much you can safely
delegate.**

### The verification ladder (cheapest to strongest)

| Level | Mechanism | Strength |
|---|---|---|
| 1 | "Run the tests and fix failures" in the prompt | Works today; agent can still rationalize |
| 2 | Explicit `done_when` criteria + **show evidence** (output, logs, screenshots) | You review evidence, not claims |
| 3 | A goal or condition re-checked by a *separate* evaluator each turn | Harder to fake "done" |
| 4 | **Deterministic gate**: a hook or CI that blocks completion until the check passes | Not negotiable by the model |
| 5 | Independent reviewer in a fresh context (Module 5) | Catches what tests don't encode |

Move tasks *up* this ladder as you give them more autonomy.

### Techniques

- **Test-first for bugs:** "Write a failing test that reproduces it, then fix it." The failing
  test is proof you understood the bug. The passing test is the exit condition.
- **Protect the oracle:** tell the agent that tests define correctness and must not be edited
  to pass. If a test seems wrong, it should *report it* rather than change it. Anthropic's docs
  include a ready-made prompt for this ("Tests are there to verify correctness, not to define
  the solution… if any of the tests are incorrect, please inform me rather than working around
  them").
- **End-to-end over unit for "done":** Anthropic's long-running-agent harness found that
  requiring browser-automation checks of real user flows caught bugs that code-level tests missed.
- **Structured status:** keep a `features.json` / `tests.json` with every requirement starting
  as `failing`. This prevents premature victory declarations.
- **Evidence, not assertions:** "Show me the command you ran and its output." Reviewing
  evidence is faster than re-verifying, and it works for sessions you didn't watch.

---

## Part B: Evals, measuring prompts and workflows

### The core workflow: error analysis first, metrics second

Hamel Husain and Shreya Shankar's approach, now the field's de facto standard, runs in this
order:

1. **Collect traces.** Gather 50–100 real interactions: inputs, outputs, tool calls, the
   whole transcript.
2. **Read them. Write free-text notes** on what went wrong in each ("open coding"). Don't start
   with a rubric.
3. **Cluster the notes into failure categories** ("axial coding"). Count them. You'll usually find
   2–4 categories that account for most failures.
4. **Build targeted evals for the top categories only.**
   - Use **code-based checks** when a deterministic rule works (JSON parses, correct tool
     called, required field present, test passes).
   - Use an **LLM judge** only for things that genuinely need judgment.
5. **Change the prompt, context, or workflow.** Re-run. Compare.
6. **Repeat**, because new failure modes appear as old ones get fixed.

Most teams skip straight to step 4 and write generic judges ("rate helpfulness 1–10").
That's backwards: you end up measuring things that aren't your actual problems.

### LLM-as-judge done right

- **Binary pass/fail per criterion**, not 1–10 scales. Scales are noisy and hard to act on.
- **One judge per failure mode**, not one mega-judge.
- **Write the critique a new employee could follow.** Include examples of pass and fail with
  reasons.
- **Calibrate against human labels.** Label ~50–100 examples yourself, then measure how often
  the judge agrees. Track true-positive *and* true-negative rates separately.
- **Read judge outputs periodically.** Judges drift and can be gamed.

### Agent eval specifics

From Anthropic's "Demystifying evals for AI agents" (Jan 2026):

- **Start small: 20–50 tasks** drawn from real failures, each with an unambiguous success
  criterion and ideally a reference solution.
- **Grade outcomes, not steps.** Agents find valid paths you didn't anticipate, so don't
  penalize them.
- **Isolate runs.** Use clean state per trial. Shared state leaks between runs.
- **pass@k vs pass^k:**
  - *pass@k* is the probability that at least one of k tries succeeds. It fits tasks where you
    can pick the best attempt.
  - *pass^k* is the probability that **all** k tries succeed. It fits anything user-facing,
    where consistency matters. As k grows, pass@k approaches 100% while pass^k can collapse. A
    prompt that "works" 80% of the time fails one user in five.
- **Watch for saturation.** When scores near 100%, the eval stops telling you anything. Add
  harder cases.
- **Beware infra noise.** Anthropic found infrastructure variability alone can move agentic
  coding scores meaningfully. Run multiple trials before believing small differences.

### A minimum viable eval harness (for one prompt)

You don't need a platform to start. A spreadsheet and a script are enough:

```
evals/
  cases.jsonl        # {id, input, notes, expected / criteria}
  run.py             # runs prompt vN over all cases, N trials each, saves outputs
  checks.py          # deterministic checks
  judge_prompt.md    # binary judge for the 1-2 fuzzy criteria
  results/
    v1.csv, v2.csv   # per-case pass/fail; diff between versions
```

Process: change one thing in the prompt, re-run, and compare **per-case** (not just the
average). Look at the cases that flipped, in both directions.

## Anti-patterns

- **Vibe-checking on one example** and shipping the change.
- **Generic metrics** (helpfulness, coherence) disconnected from your actual failures.
- **Letting the model grade itself** in the same context (Module 5).
- **Averages only.** A +3% average can hide a regression on your most important case type.
- **Evals as a one-time project.** They're living artifacts. Add every new production
  failure as a case.

## Exercises

1. **Error analysis (2 hours, the most valuable exercise in this syllabus).** Take 30–50 real
   outputs from a prompt you care about, whether a user-facing bot, a recurring agent task, or
   a code-gen prompt. Write a one-line note on each failure. Cluster the notes. What are your
   top three failure modes?
2. **Build 20 cases** targeting the top failure mode. Write a deterministic check if possible,
   or a binary judge if not.
3. **A/B one change.** Pick one prompt modification. Run both versions 3× each over the 20 cases.
   Report pass^3, not just the average.
4. **Agent verification upgrade.** For your most common agent task, move it one rung up the
   verification ladder (for example, add a Stop hook or CI gate).

## Sources

- Anthropic: [Demystifying evals for AI agents (Jan 2026)](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)
- Anthropic: [Quantifying infrastructure noise in agentic coding evals (Feb 2026)](https://www.anthropic.com/engineering)
- Anthropic: [Best practices for Claude Code, "Give Claude a way to verify its work"](https://code.claude.com/docs/en/best-practices)
- Anthropic: [Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- Hamel Husain: [AI Evals: Everything You Need to Know (FAQ)](https://hamel.dev/blog/posts/evals-faq/)
- Hamel Husain: [Using LLM-as-a-Judge for Evaluation: A Complete Guide](https://hamel.dev/blog/posts/llm-judge/)
- Lenny's Newsletter: [Evals, error analysis, and better prompts (Hamel Husain)](https://www.lennysnewsletter.com/p/evals-error-analysis-and-better-prompts)
- Pragmatic Engineer: [A pragmatic guide to LLM evals for devs](https://newsletter.pragmaticengineer.com/p/evals)
- [How to Correctly Report LLM-as-a-Judge Evaluations (2025)](https://arxiv.org/abs/2511.21140)
