# Module 5: Multi-agent systems and reviewer agents

## Why it matters

You've tried reviewer agents and responsibility separation and got mixed results. That's
the common experience, and the evidence explains why. Multi-agent setups help in specific,
identifiable conditions and hurt outside them. Most "architect / developer / reviewer / QA"
role setups fall outside them.

## Core ideas

### 1. Start with the simplest thing; add agents only for a measured reason

Anthropic's "Building effective agents" (Dec 2024) remains the canonical framing:

- **Workflows** are LLM calls orchestrated along predefined paths: prompt chaining, routing,
  parallelization, orchestrator-workers, and evaluator-optimizer.
- **Agents** are LLMs dynamically directing their own tool use in a loop.
- *Start with a single LLM call. Add complexity only when it demonstrably improves outcomes.*

The same rule applies to agent counts. A single well-contexted agent with good verification
beats a committee of under-contexted ones.

### 2. When multi-agent *does* work

**Breadth-first, parallelizable, read-heavy work.** Anthropic's multi-agent research
system (Opus lead + Sonnet subagents) outperformed a single Opus agent by **90.2%** on
their internal research eval. Their analysis is revealing: **token usage alone explained
~80% of performance variance** on BrowseComp. Much of the gain comes from *spending more
tokens across more fresh context windows in parallel*. The cost is about **15×** the tokens
of a chat.

They also list where it's a **poor fit**:
- tasks needing **shared context** across agents
- work with **high interdependency** between parts
- **most coding tasks**, which have fewer truly parallelizable pieces than research
- low-value tasks where the token cost isn't worth it

**Massive parallel work with a near-perfect oracle.** Anthropic built a C compiler with 16
parallel agents (~2,000 sessions, ~$20k). It worked because a **high-quality test suite**
coordinated everyone. The author's lesson: *"it's important that the task verifier is nearly
perfect."* Where tasks weren't independent, they used GCC as a comparison oracle.

**Manager-and-workers with full context sharing.** Cognition, the Devin team, wrote "Don't
Build Multi-Agents" in 2025. By 2026 they report some setups *do* work, mainly
**map-reduce-and-manage**: a manager splits the work, children execute, and the manager
synthesizes. Their lessons:
- Agents **assume** they share state with their children when they don't.
- Cross-agent communication doesn't happen by default, because models weren't trained for it.
- To avoid compounding errors, share **complete action histories**, not task summaries.

### 3. Why responsibility separation often disappoints

Cognition's original argument still holds: **actions carry implicit decisions.** When two
agents build parts of the same thing with separate context, each makes reasonable but
**conflicting** assumptions (their example: one subagent builds a Mario-style background,
another builds a bird that doesn't match). Human-style role separation (architect,
developer, QA) works for humans because they share an enormous background context. Agents
don't. Every handoff is a lossy summary.

**Split along context and verification boundaries, not org-chart roles:**
- ✅ "Explore the auth module and report back" (isolates *read-heavy noise*)
- ✅ "Review this diff against SPEC.md in a fresh context" (isolates *judgment from authorship*)
- ✅ "Migrate these 200 independent files" (truly *independent units*)
- ❌ "Architect agent designs, developer agent implements, QA agent tests" on one tightly
  coupled feature (each handoff loses decisions)

Role prompts ("you are a senior architect") also don't add knowledge or accuracy (Module 1).
They change tone, not competence.

### 4. Why reviewer agents disappoint, and how to fix each cause

| Cause | What happens | Fix |
|---|---|---|
| **Self-evaluation bias** | Generators praise their own mediocre work | Separate generator and evaluator; never let the author grade itself |
| **Shared context → anchoring** | A reviewer that saw the reasoning accepts its framing | Reviewer runs in a **fresh context** and sees only the diff plus criteria |
| **Correlated blind spots** | Same model family misses the same bug class | Different model or vendor for review; deterministic checks for known classes |
| **No criteria** | Reviewer reviews against its own taste → style nits | Give it the **spec, plan, and explicit checklist** |
| **Asked to find problems, so it always finds some** | Endless findings → over-engineering, defensive code, tests for impossible cases | Tell it to report **only** issues affecting correctness or stated requirements; everything else is optional |
| **Uncalibrated leniency** | Evaluator waves through subtle bugs | Few-shot calibrate with graded examples; tune toward skepticism |
| **Findings without evidence** | Plausible-sounding but wrong issues | Require a **failing test, repro, or line reference** per finding |
| **Unbounded loops** | Writer and reviewer ping-pong forever | Cap rounds; escalate to a human on disagreement |

From Anthropic's Claude Code guide:

> *"A reviewer prompted to find gaps will usually report some, even when the work is sound…
> Chasing every finding leads to over-engineering… Tell the reviewer to flag only gaps that
> affect correctness or the stated requirements, and treat the rest as optional."*

From their 2026 long-running-harness work: a **separate** evaluator is easier to tune toward
skepticism than making a generator self-critical. Turning subjective quality into
**concrete, gradable criteria** was what made it work. And a **"sprint contract"**, where
generator and evaluator agree on what "done" means *before* implementation, bridged the
spec-to-test gap.

### 5. Harness components encode assumptions, so re-test them

Anthropic's key meta-lesson: *"every component in a harness encodes an assumption about what
the model can't do on its own."* When models improve, some scaffolding becomes dead weight.
For example, context resets were needed for "context anxiety" on one model generation and
not the next. Periodically **remove** a component and measure.

## Techniques

### A reviewer prompt that works

```
Review the diff for <feature> against SPEC.md in a fresh context.
You did not write this code. Your job is to find defects that would matter in production.

Report ONLY:
- requirements in SPEC.md that are missing or incorrectly implemented
- correctness bugs (wrong results, crashes, races, data loss, security)
- changes outside the stated scope

For each finding: file:line, the concrete failure scenario (input → wrong output),
and severity (blocking / non-blocking). If you can, write a failing test.
Do NOT report style preferences, speculative refactors, or "consider adding" items.
If you find nothing blocking, say so plainly. That's a valid outcome.
```

### Writer/tester separation (TDD across agents)

Agent A writes tests from the spec (without seeing implementation). Agent B implements until
the tests pass (without editing the tests). This works because the **tests are the shared,
verifiable contract**, not a lossy summary.

### Cross-model review

Having a different vendor's model review the diff is a cheap way to decorrelate blind spots.
Early 2026 studies and practitioner reports suggest it catches issue classes the authoring
model misses. Treat it as one more signal, not an oracle.

## Anti-patterns

- **Org-chart agents** on tightly coupled work.
- **Reviewer in the same session** as the author ("now review your code").
- **Unfiltered reviewer output** fed straight back to the author, producing over-engineering.
- **Multi-agent for token-cheap, sequential tasks.** That's 15× the cost for no gain.
- **Letting the model spawn subagents freely.** Recent models over-delegate (for example,
  spawning a subagent where a single grep would do). Give explicit guidance on when
  delegation is warranted.

## Exercises

1. **Audit your reviewer setup.** Walk through the "causes" table. Which rows apply to your
   past setup? (Likely: shared context, no criteria, unfiltered findings.)
2. **Calibrated reviewer.** Rewrite your reviewer prompt using the template above. Run it on
   5 past diffs where you *know* the real bugs. Count true finds vs noise, then compare with
   your old prompt.
3. **One-agent vs many.** Take a task you'd have split across roles. Do it once with a single
   agent that has a good spec and verification, and once with your multi-agent setup. Compare
   quality, corrections, and tokens.
4. **Cross-model check.** Have a different vendor's model review three diffs. Note any finding
   the authoring model's reviewer missed.

## Sources

- Anthropic: [Building effective agents (Dec 2024)](https://www.anthropic.com/research/building-effective-agents)
- Anthropic: [How we built our multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system)
- Anthropic: [Building a C compiler with a team of parallel Claudes (Feb 2026)](https://www.anthropic.com/engineering/building-c-compiler)
- Anthropic: [Harness design for long-running application development (Mar 2026)](https://www.anthropic.com/engineering/harness-design-long-running-apps)
- Anthropic: [Best practices for Claude Code, "Add an adversarial review step"](https://code.claude.com/docs/en/best-practices)
- Cognition: [Don't Build Multi-Agents (2025)](https://cognition.com/blog/dont-build-multi-agents)
- Cognition: [Multi-Agents: What's Actually Working (2026)](https://cognition.com/blog/multi-agents-working)
- [Cross-Context Review: Separating Production and Review Sessions (2026)](https://arxiv.org/html/2603.12123)
- [Cross-Model LLM Code Review: Should you use Claude to review Codex or vice versa? (2026)](https://arxiv.org/html/2607.21656v1)
