# Module 4: Agentic coding workflows, delegating without micromanaging

## Why it matters

You've experienced both extremes. Micromanaging works but doesn't scale. Hands-off
produces "almost right, but not quite." The fix isn't a point on a slider between the two.
It's a **workflow** that front-loads your judgment (spec, plan) and back-loads the agent's
effort (implementation against verifiable checks). Your time goes into the parts only you
can do, and the agent gets autonomy only where it can be checked.

## Core ideas

### 1. Autonomy should match verifiability and specification

A useful 2×2:

|  | **Easy to verify** | **Hard to verify** |
|---|---|---|
| **Well-specified** | Delegate fully. Walk away. | Delegate, with an independent review step |
| **Under-specified** | Spec first (interview), then delegate | Pair closely, or do it yourself with AI assistance |

Most disappointing hands-off runs sit in the bottom-right quadrant with the autonomy
setting of the top-left.

### 2. Explore → Plan → Implement → Verify → Commit

This is Anthropic's recommended workflow for Claude Code, and the shape is the same in
Codex, Cursor, and other harnesses:

1. **Explore** (read-only / plan mode): "Read `src/auth` and explain how sessions and
   login work."
2. **Plan:** "I want to add Google OAuth. What files change? What's the session flow? Write
   a plan." **Edit the plan yourself.** This is your highest-leverage review point, because
   fixing a plan is cheap and fixing a diff is expensive.
3. **Implement** against the plan, with verification: "Implement it, write tests for the
   callback handler, run the suite, fix failures."
4. **Verify** independently (Module 3 ladder, Module 5 reviewer).
5. **Commit**, with a message and PR.

**Skip planning** when you could describe the diff in one sentence. Planning is overhead on
trivial tasks.

### 3. For anything big: spec first, in a separate session

Spec-driven development (SDD) went mainstream in 2025–26 (GitHub Spec Kit, AWS Kiro,
OpenSpec, and plan modes in every major harness). The core idea is simple:

> Agents are great at writing code and terrible at guessing what you meant. The problem
> isn't generation speed, it's **drift**.

A lightweight version that works in any tool:

```
I want to build [brief description]. Interview me in detail using AskUserQuestion.
Ask about technical implementation, UI/UX, edge cases, concerns, and tradeoffs.
Don't ask obvious questions; dig into the hard parts I might not have considered.
Keep going until we've covered everything, then write a complete spec to SPEC.md.
```

Then **start a fresh session** to implement from `SPEC.md`. According to Anthropic's guide, the
best specs:
- name the files and interfaces involved
- say explicitly what's **out of scope**
- end with an **end-to-end verification step** that proves the feature works

*"Time spent making the spec precise pays off more than time spent watching the
implementation."* That's the answer to your micromanagement problem: move your attention
**earlier**.

A caution on SDD: heavyweight spec frameworks can become their own bureaucracy. Use the
lightest spec that removes ambiguity.

### 4. Advisory vs deterministic controls

| Mechanism | Nature | Use for |
|---|---|---|
| Prompt / CLAUDE.md / AGENTS.md | Advisory; the model *usually* follows it | Conventions, preferences, context |
| Skills | Advisory, loaded on demand | Domain knowledge and workflows needed sometimes |
| **Hooks** | **Deterministic**; a script runs every time | Anything that must happen with zero exceptions: format on save, block edits to `migrations/`, run tests before stop |
| Permissions / sandbox | Deterministic | Safety boundaries (Module 8) |
| CI | Deterministic, external | The final gate |

If you catch yourself writing "ALWAYS run the linter" in a context file for the third time,
**make it a hook.**

### 5. Course-correct early; restart rather than argue

- Interrupt as soon as it goes off track (Esc in Claude Code). Context is preserved.
- Use checkpoints and rewind to try risky approaches cheaply.
- **After two failed corrections on the same issue, stop.** The context is now full of failed
  approaches. Clear it and write a better initial prompt that includes what you learned. *"A
  clean session with a better prompt almost always outperforms a long session with
  accumulated corrections."*

### 6. Watch for known model tendencies, and counter them explicitly

Current frontier coding models have documented default behaviors worth prompting against
when you see them:

- **Over-engineering:** extra files, abstractions, config options, and defensive code for
  impossible cases. Counter with an explicit minimalism instruction (see below).
- **Test-gaming:** making tests pass by special-casing or hard-coding. Counter with "tests
  verify correctness, they don't define the solution; report bad tests instead of working
  around them."
- **Scratch-file litter:** ask it to clean up temporary files.
- **Speculating about unread code:** instruct it to "never make claims about code you haven't
  opened."
- **Risky actions** (force push, deleting branches, posting externally): state a reversibility
  policy (Module 8).

Anthropic's docs ship a sample "avoid over-engineering" block worth adapting:

```
Only make changes that are directly requested or clearly necessary.
- Scope: don't add features, refactor, or "improve" beyond what was asked.
- Docs: don't add comments/docstrings to code you didn't change.
- Defensive coding: don't add error handling for scenarios that can't happen;
  validate only at system boundaries.
- Abstractions: don't create helpers for one-time operations or design for
  hypothetical future requirements.
```

### 7. Long-running and parallel work

Once a single session is reliable:
- **Headless / non-interactive mode** (`claude -p`, `codex exec`) for scripted fan-out over
  lists of files, with narrowly scoped tool permissions. **Test on 2–3 items, refine the
  prompt, then run on all.**
- **Parallel sessions in git worktrees** for independent tasks.
- **Multi-session harnesses** for very long work: an initializer session writes `init.sh`,
  `features.json` (all failing), and `progress.txt`. Each later session reads progress, picks
  **one** feature, implements it, verifies it end-to-end, commits, and updates progress.
  Anthropic reports multi-hour runs this way. Their 2026 report cites a single 7-hour run
  across a 12.5M-line codebase.

## Techniques: a delegation checklist

Before handing off a non-trivial task, check that you have:

- [ ] **Goal + why** in 2–3 sentences
- [ ] **Scope**, including what's out of scope
- [ ] **Pointers**: relevant files, example patterns to follow, and docs
- [ ] **Constraints** that differ from defaults
- [ ] **Done-when**: runnable checks, plus the evidence to show
- [ ] **Autonomy level**: what it may do without asking, and what needs confirmation
- [ ] **Plan review**: for multi-file changes, have you read and edited its plan?

## Anti-patterns

- **"Build me X" with no spec, then 40 turns of corrections.** Spend 15 minutes on the spec
  instead.
- **Watching the implementation instead of reviewing the plan.** That puts your attention at
  the lowest-leverage point.
- **One mega-session for a whole feature, including debugging tangents.**
- **Advisory rules for invariants.** If it must hold, enforce it with a hook or CI.
- **Accepting "done" without evidence.**
- **Unscoped investigation** ("look into why it's slow") that reads 200 files into your
  main context. Scope it or use a subagent.

## Exercises

1. **Interview → spec → fresh session.** Pick a medium feature (half a day of work). Run the
   interview prompt, edit `SPEC.md` yourself, then implement it in a new session. Compare the
   number of corrections to your usual approach.
2. **Plan editing.** On your next multi-file change, force a plan and edit it before
   approving. Note what you changed. Those changes are the misunderstandings you avoided.
3. **Hook one invariant.** Pick a rule you repeat most often ("run the formatter", "don't touch
   generated files") and implement it as a hook.
4. **Two-strikes rule.** For one week, whenever you correct the same issue twice, clear and
   restart. Keep a tally of outcomes.

## Sources

- Anthropic: [Best practices for Claude Code](https://code.claude.com/docs/en/best-practices), covering explore-plan-code, interview-to-spec, hooks vs CLAUDE.md, course correction, fan-out, and failure patterns
- Anthropic: [Prompting best practices, "Agentic systems"](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices), covering over-eagerness, test-gaming, hallucination, and multi-window workflows
- Anthropic: [Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- Anthropic: [Harness design for long-running application development (Mar 2026)](https://www.anthropic.com/engineering/harness-design-long-running-apps)
- Anthropic: [2026 Agentic Coding Trends Report, summary](https://tessl.io/blog/8-trends-shaping-software-engineering-in-2026-according-to-anthropics-agentic-coding-report)
- Microsoft: [Spec-Driven Development: A Spec-First Approach to AI-Native Engineering](https://developer.microsoft.com/blog/spec-driven-development-ai-native-engineering/)
- OpenAI: [Codex prompting guide](https://github.com/openai/openai-cookbook/blob/main/examples/gpt-5/codex_prompting_guide.ipynb)
- Simon Willison: [Live blog, Code w/ Claude 2026](https://simonwillison.net/2026/May/6/code-w-claude-2026/)
