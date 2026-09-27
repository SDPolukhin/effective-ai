# Module 0: A working mental model

## Why it matters

Most frustration with LLMs comes from a wrong mental model. If you picture a colleague
who "understands" the project, you'll under-specify. If you picture autocomplete, you'll
over-specify and micromanage. A model that predicts your failures lets you design around
them in advance.

## Core ideas

### 1. The model only knows what's in the context window (plus its training)

Every turn, the model re-reads the entire context (system prompt, tool definitions,
conversation, file contents, tool outputs) and produces the next tokens. It has no other
memory. "Memory" features, CLAUDE.md/AGENTS.md files, and skills all work by **putting text
back into the context window**.

**Consequence:** If a fact isn't in the window, the model will either guess or go looking
for it. Guessing produces fluent, confident, wrong output, and that's what hallucination
looks like from the outside.

### 2. Context is a finite resource that degrades before it runs out

Performance drops as input length grows, well before the advertised limit. Chroma's
"Context Rot" study tested 18 frontier models, and **every one got worse as input length
increased**. The decline is gradual, not a cliff, and it's worse when distractors are
semantically similar to the target. Anthropic's Claude Code guide opens with the same point:
*"LLM performance degrades as context fills… The context window is the most important
resource to manage."*

**Consequence:** More context isn't free. Irrelevant context actively hurts.

### 3. The model is trained to finish, and it finishes when the work *looks* done

Agentic models are trained to complete tasks. Without an external signal (tests, a build, a
screenshot, a checker), "looks done" is the only stopping criterion available. That's why
agents declare victory early, mark features complete without testing, and edit tests
until they pass. It isn't malice. The loop simply has no other exit condition.

**Consequence:** **Verification is the primary lever for autonomy.** Module 3 covers it in depth.

### 4. Self-evaluation is biased; fresh-context evaluation is less so

Asked to grade their own work, agents *"respond by confidently praising the work — even
when, to a human observer, the quality is obviously mediocre"* (Anthropic, *Harness design
for long-running apps*, 2026). A reviewer that shares the author's context inherits the
author's framing. A reviewer from the same model family shares many of its blind spots.

**Consequence:** Reviewer agents help only when they're **independent** (fresh context,
explicit criteria) and **calibrated** (told what counts as a real finding). Module 5 covers this.

### 5. Instruction-following is literal, and it's getting more literal

Newer frontier models follow instructions more precisely than older ones. Anthropic's
current docs note that "can you suggest some changes" gets suggestions, not edits. Prompts
written to push older, lazier models ("ALWAYS use tool X", "be extremely thorough") now
cause **over-triggering** and **over-engineering**.

**Consequence:** Prompts are model-version-specific artifacts. Re-test them when you upgrade.

### 6. Perceived productivity is not measured productivity

METR's 2025 randomized trial found experienced open-source developers were **19% slower**
with AI tools, even though they *believed* they were about 20% faster. The early-2026
follow-up (57 developers, 800+ tasks) estimated −18% (CI −38% to +9%) for returning
developers and −4% for new recruits. METR says tools are probably improving but that they
can no longer measure it cleanly, because many developers now refuse to work without AI at
all. Survey data shows the same gap. In Stack Overflow's 2025 survey, 84% of developers use
AI, only 29% trust its output, and the top frustration (66%) is *"solutions that are almost
right, but not quite."*

**Consequence:** Your gut sense of "that went well" is a biased instrument. That's another
reason to measure (Module 3).

## The three failure sources

Every bad outcome you've had likely traces to one of these:

| Failure source | Symptom | Fix lives in |
|---|---|---|
| **No verification signal** | "Done!", but it isn't; tests edited to pass; plausible-but-wrong code | Module 3, Module 4 |
| **Missing or noisy context** | Solves the wrong problem; ignores conventions; forgets rules mid-session | Module 1, Module 2 |
| **No measurement** | "I don't know if my prompt is good"; changes feel random | Module 3, Module 7 |

## A useful framing: the capable new hire

Anthropic's docs describe the model as *"a brilliant but new employee who lacks context on
your norms and workflows."* Their **golden rule** is to show your prompt to a colleague with
minimal context. If they'd be confused, the model will be too.

This framing tells you what to include:
- the **goal and the why**, since the model generalizes from reasons
- **constraints you'd otherwise assume are obvious**
- **what "done" looks like** and **how to check it**
- **where to look** for more context

It also tells you what to leave out:
- generic best practices ("write clean code"), which it already knows
- file-by-file tours of the codebase, which it can read itself

## Anti-patterns

- **Anthropomorphic trust.** "It said it tested it" is not evidence. Ask for evidence: the
  command output, the test log, the screenshot.
- **The long-session illusion.** Treating a 3-hour session as accumulated shared understanding.
  It's accumulated noise, plus a lossy summary once compaction kicks in.
- **Magic-phrase hunting.** Searching for incantations instead of fixing context or
  verification. The Wharton "Prompting Science Reports" found threats and tips don't reliably
  help, expert personas don't improve factual accuracy, and "think step by step" adds little
  to reasoning models.

## Exercises

1. **Failure audit (30 min).** List your last five disappointing LLM or agent outcomes.
   Classify each as *verification*, *context*, or *measurement*. Count them.
2. **Colleague test.** Take a prompt you used recently. Imagine handing it, with nothing else,
   to a smart contractor on their first day. Write down every question they'd ask. Those
   questions are your missing context.
3. **Speed check.** On your next three agent tasks, write down your estimate of time saved.
   Then look honestly at the time spent reviewing and fixing afterwards.

## Sources

- Chroma: [Context Rot: How Increasing Input Tokens Impacts LLM Performance](https://www.trychroma.com/research/context-rot)
- Anthropic: [Best practices for Claude Code](https://code.claude.com/docs/en/best-practices)
- Anthropic: [Harness design for long-running application development](https://www.anthropic.com/engineering/harness-design-long-running-apps)
- Anthropic: [Prompting best practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)
- METR: [Measuring the impact of early-2025 AI on experienced OS developer productivity](https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/)
- METR: [We are changing our developer productivity experiment design (Feb 2026)](https://metr.org/blog/2026-02-24-uplift-update/)
- Stack Overflow: [2025 Developer Survey](https://survey.stackoverflow.co/2025/), [Closing the developer AI trust gap](https://stackoverflow.blog/2026/02/18/closing-the-developer-ai-trust-gap/)
- Wharton GAIL: [Prompting Science Report 2: The Decreasing Value of Chain of Thought](https://gail.wharton.upenn.edu/research-and-insights/tech-report-chain-of-thought/), [Report 4: Expert Personas Don't Improve Factual Accuracy](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=5879722)
