# effective-ai

A syllabus for working effectively with LLMs, aimed at the **user** side: prompting,
context engineering, agentic coding harnesses, multi-agent setups, RAG, and evaluating
whether any of it actually works. It was compiled in September 2026 from primary sources:
lab documentation, engineering write-ups, and peer-reviewed or preprint research. Links are
in each module and in [SOURCES.md](SOURCES.md).

## Who this is for

You've used LLMs, including agentic harnesses on a couple of projects. You've tried letting
agents run with less supervision, adding reviewer agents, and splitting responsibilities,
and the results were mixed. You're not sure
whether your prompts are good, and prompts that an LLM wrote for you didn't work well,
especially in user-facing sessions.

## The one-paragraph thesis

The bottleneck is almost never a missing "magic phrase." It's one of three things:

1. **The model can't tell when it's wrong.** There's no verification signal.
2. **The model doesn't know what you know.** There's no spec, the context is missing, or
   it's buried in noise.
3. **You can't tell whether a change helped.** There are no evals.

Everything in this syllabus is a technique for fixing one of those three. Autonomy should
grow only as fast as you can cheaply verify the result.

## Modules

| # | Module | Core question |
|---|--------|---------------|
| 0 | [Mental model](syllabus/00-mental-model.md) | What is an LLM actually doing, and why does it fail the way it does? |
| 1 | [Prompting fundamentals](syllabus/01-prompting-fundamentals.md) | What in prompting is evidence-based, and what's folklore? |
| 2 | [Context engineering](syllabus/02-context-engineering.md) | What should be in the context window, and when? |
| 3 | [Verification and evals](syllabus/03-verification-and-evals.md) | How do you measure prompt quality instead of guessing? |
| 4 | [Agentic coding workflows](syllabus/04-agentic-coding.md) | How do you delegate to a coding agent without micromanaging *or* babysitting? |
| 5 | [Multi-agent systems and reviewers](syllabus/05-multi-agent-and-review.md) | When do reviewer agents and role separation help, and when do they hurt? |
| 6 | [RAG and retrieval](syllabus/06-rag-and-retrieval.md) | How do you give models the right knowledge at the right time? |
| 7 | [Prompts for user-facing products](syllabus/07-user-facing-prompts.md) | How do you write, and machine-optimize, system prompts that face real users? |
| 8 | [Failure modes and security](syllabus/08-failure-modes-and-security.md) | Sycophancy, reward hacking, and prompt injection: what to design around? |
| 9 | [Practice plan](syllabus/09-practice-plan.md) | A four-week plan for turning this into habit |

**Suggested order:** 0 → 1 → 3 → 2 → 4 → 5, then 6, 7, and 8 as they apply to your work,
and finish with 9. Module 3 comes early on purpose: every later module assumes you can
measure.

## Studying with Claude Code

`CLAUDE.md` sets up tutoring sessions and `notes/progress.md` tracks progress between them.
Start a fresh session per module (or short pair) and say "continue", or name a module. The
session reads the progress file, teaches the module interactively using your own work, and
updates the progress file when it's done.

## How each module is laid out

- **Why it matters**: the failure it prevents
- **Core ideas**: the concepts, with evidence
- **Techniques**: concrete moves you can use today
- **Anti-patterns**: things that feel productive but aren't
- **Exercises**: short, hands-on drills on your own work
- **Sources**: primary references

## A note on shelf life

Model-specific advice goes stale with each release. For example, "tell it to be thorough"
helped older models but makes newer ones over-trigger. The principles in this syllabus
(verification, context hygiene, measurement) have held steady across model generations.
When a technique is tied to a particular model's behavior, the module says so.
