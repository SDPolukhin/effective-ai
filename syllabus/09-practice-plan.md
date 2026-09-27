# Module 9: A four-week practice plan

Reading changes little. Practice changes habits. This plan assumes about 3–5 hours a week
on top of your normal work, using your real tasks rather than toy examples.

## Week 1: Baseline and measurement

**Goal:** Know where your failures come from, and have one eval.

- [ ] Module 0 exercise: **failure audit.** Classify your last 5–10 bad outcomes as
      verification, context, or measurement.
- [ ] Module 3 exercise: **error analysis** on 30–50 outputs from one prompt or workflow you
      care about. Find your top three failure modes.
- [ ] Build a **20-case eval** for the top failure mode, with a deterministic check if possible.
- [ ] Module 1 exercise: **folklore A/B.** Strip personas, CAPS, and "think step by step" from
      one prompt and run both versions on your 20 cases.

**Checkpoint:** Can you say, with a number, whether a prompt change helped?

## Week 2: Context and verification in agentic work

**Goal:** Agent sessions that end with evidence, not claims.

- [ ] Module 2: **CLAUDE.md / AGENTS.md diet.** Cut every line that doesn't prevent a mistake.
- [ ] Module 2: move one situational instruction block into a **skill**.
- [ ] Module 4: turn your most-repeated rule into a **hook**.
- [ ] Module 3: move your most common agent task **one rung up** the verification ladder.
- [ ] Practice the **two-strikes rule** all week: after two failed corrections, clear and
      restart with a better prompt.

**Checkpoint:** On routine tasks, how often do you now accept "done" without re-checking
yourself?

## Week 3: Delegation at scale

**Goal:** Hand off a half-day feature and get it back mergeable.

- [ ] Module 4: **interview → SPEC.md → fresh session** on one real feature.
- [ ] **Edit the plan** before implementation, and log what you changed.
- [ ] Module 5: rewrite your **reviewer prompt** with criteria, a severity filter, and
      evidence requirements. Validate it on five past diffs with known bugs.
- [ ] Module 5: try **one-agent-with-spec vs your multi-agent setup** on comparable tasks.
- [ ] Module 8: **trifecta audit** and a **reversibility policy** in your global config.

**Checkpoint:** What fraction of your time went to spec and plan versus watching and
correcting? The goal is to shift it toward spec and plan.

## Week 4: User-facing prompts and retrieval

**Goal:** Improve one user-facing prompt measurably.

- [ ] Module 7: **deconstruct** an LLM-written prompt, then rewrite it as a briefing.
- [ ] Module 7: **user simulator** with three personas, 8 turns each. Error-analyze the results.
- [ ] Module 7: **evidence-driven revision** using the meta-prompt. Report pass^3 before and after.
- [ ] Module 6 (if applicable): **retrieval-only eval**. Is retrieval or generation your
      bottleneck?

**Checkpoint:** Do you have a versioned prompt with eval results attached, and a process
for adding new failures?

## Ongoing habits

- **Weekly:** read 10–20 real transcripts or agent sessions. Add new failures to your evals.
- **Per model upgrade:** re-run evals. Look for over-triggering from old "be thorough"
  instructions and delete what's no longer needed.
- **Per new tool or MCP server:** trifecta check.
- **Monthly:** prune CLAUDE.md / AGENTS.md / system prompts. Remove a harness component and
  measure whether it still earns its place.

## Staying current

These sources consistently publish practical, evidence-backed material:
- [Anthropic Engineering blog](https://www.anthropic.com/engineering) and [Claude prompting docs](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices), which include model-specific pages per release
- [OpenAI Cookbook](https://cookbook.openai.com/) and [latest-model guidance](https://developers.openai.com/api/docs/guides/latest-model)
- [Simon Willison's blog](https://simonwillison.net/), especially on agents, security, and tool releases
- [Hamel Husain's blog](https://hamel.dev/), on evals and error analysis
- [METR](https://metr.org/research/), on measured capability and productivity
- [Chroma research](https://www.trychroma.com/research), on retrieval and long-context
- [Wharton Generative AI Labs](https://gail.wharton.upenn.edu/research-and-insights/), on the empirical science of prompting
