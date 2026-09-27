# Module 7: Prompts for user-facing products

## Why it matters

User-facing system prompts are the hardest prompts to get right:
- The input distribution is **open-ended**, since users say anything.
- Conversations are **multi-turn**, and behavior drifts over turns.
- Failures are **public**, and consistency matters: pass^k, not pass@k.
- You can't correct the model mid-conversation the way you can in your own session.

This is also where LLM-written prompts fail most visibly. That isn't because models can't
write prompts. It's because they're usually asked to do it **without the information that
makes a prompt good**.

## Core ideas

### 1. Why "have the LLM write my prompt" disappoints

When you ask a model to "write a system prompt for a customer-support bot," you typically get:
- a **generic rule list** ("You are a helpful, friendly assistant… ALWAYS… NEVER…")
- **emphatic formatting** (CAPS, "CRITICAL") that makes current models over-trigger
- **boilerplate** the model would do anyway, which dilutes the rules that matter
- **no knowledge of your actual failure modes**, because it has never seen your traffic
- **style copied from older prompting advice** in its training data, advice that has since
  decayed (Module 1)

In short, it optimizes for looking like a good prompt, not for performing well on your
inputs. It had no way to do the latter.

**LLMs are good prompt *editors* when given evidence.** The fix is to give the model what a
human prompt engineer would use: the current prompt, **real failing transcripts**, the
**desired behavior** for those cases, and a way to **test** the revision. That's exactly the
loop that automated optimizers formalize (see §5).

### 2. Both labs now say: lean, outcome-first prompts

The latest guidance from both major labs converges:
- **OpenAI (GPT-5.x guides, 2025–26):** Precise instruction-followers are *more* harmed by
  contradictory or vague instructions, because they spend reasoning tokens trying to
  reconcile them. The 2026 guidance pushes **outcome-first prompting**: define what good looks
  like, set stopping conditions, and get out of the way. It says to trim repeated rules, style
  instructions that don't change behavior, examples that do nothing, and prescribed thinking
  steps. OpenAI reports lean system prompts improved internal coding-agent evals by roughly
  10–15%.
- **Anthropic:** Explain the *why* so the model can generalize. Dial back emphatic language
  and blanket "always use X" instructions that cause over-triggering. Use examples for format
  and tone.

### 3. Write the prompt as a briefing, not a rulebook

A system prompt that works reads like onboarding notes for a smart new hire:

```
## Who you're working for and why
[Product, company, what users come here to do, what success looks like for them]

## Who the users are
[Expertise level, typical goals, emotional state, what they usually don't know]

## How to help
[Principles, each with the reasoning behind it. Prose over bullet-point rules.]

## Situations that need care
[Out-of-scope requests, ambiguity, upset users, requests you can't fulfill,
 when to escalate to a human, with what to do and why]

## Tools and knowledge
[What each tool is for and when to use it. Where facts come from.
 What to do when the answer isn't available.]

## Format
[Channel constraints (chat widget, voice, email), length, tone, with examples]

## Hard limits
[A few true invariants, each with its reason. Few enough that each one matters.]
```

The principle: **a model that understands the situation handles cases you didn't enumerate;
a model with only rules fails on everything outside them.**

### 4. Test like users behave, not like you behave

- **Build a case set from real conversations** (Module 3 error analysis). If you have no
  traffic yet, write personas and have a model **simulate users**: confused, terse, hostile,
  non-native speakers, and people who change their minds mid-conversation.
- **Test multi-turn.** Many failures only appear on turn 5+: persona drift, forgotten
  constraints, sycophantic reversals after pushback.
- **Include adversarial cases:** off-topic requests, attempts to extract the system prompt,
  prompt injection via pasted content (Module 8), and requests near policy lines.
- **Measure pass^k.** Run each case several times. A behavior that happens 1 in 5 times *will*
  happen to your users.
- **Check for sycophancy under pushback.** Research in 2025–26 shows models abandon correct
  answers when users push back, and users rarely notice. Training or prompting for warmth and
  personalization increases agreeableness. Include cases where the user is wrong and insists.

### 5. Automated prompt optimization is now practical, with an eval

If you have a metric, you can let an optimizer search prompt space:
- **GEPA** (Genetic-Pareto, in DSPy; ICLR 2026 oral) reads execution traces and failure
  reasons in natural language, proposes targeted prompt edits, and keeps a Pareto frontier
  of candidates. It reports beating MIPROv2 by ~14% and RL (GRPO) by up to 20% with up to
  35× fewer rollouts.
- **Vendor tools:** OpenAI's prompt optimizer (see the cookbook) and Anthropic's Console
  prompt improver / generator are good for a first draft or a structured rewrite. They still
  need your eval set to confirm the result.

The pattern that matters, whether manual or automated:

```
current prompt + failing traces + desired behavior → proposed edit → run eval → keep if better
```

Without the eval, an optimizer (human or machine) is just generating plausible-looking
variants, which is exactly the experience you've had.

### 6. Treat prompts as code

- **Version control** prompts, with the eval results for each version.
- **Change one thing at a time** and compare per-case (Module 3).
- **Re-run evals on every model upgrade.** Instructions that fixed an older model's laziness
  can cause the new one to over-trigger.
- **Monitor production:** sample and read real transcripts weekly, and add new failures to
  the case set.

## Techniques: a meta-prompt that actually works

When you want a model's help revising a system prompt, give it evidence:

```
<current_prompt>…</current_prompt>

<failures>
<case id="1">
<transcript>…</transcript>
<what_went_wrong>Agent promised a refund it can't issue.</what_went_wrong>
<desired>Explain that refunds go through billing, offer to open a ticket.</desired>
</case>
… (5–15 cases, diverse)
</failures>

<passing_cases_to_preserve>… (a few that currently work) …</passing_cases_to_preserve>

Diagnose the root cause of each failure (missing context? ambiguous instruction?
conflicting rules? missing example?). Propose the *smallest* edits to the prompt that fix
them without breaking the passing cases. Prefer adding context and reasons over adding
rules. Prefer deleting a conflicting instruction over adding a counter-instruction.
Output a diff and a one-line rationale per change.
```

Then run your eval. Accept only the edits that measurably help.

## Anti-patterns

- **"Write me a system prompt for X"** with no traces, no users, and no eval.
- **Patching with CAPS.** Each failure gets a new `IMPORTANT:` line, until nothing is important.
- **Contradictions accumulated over time** ("be concise" in one section, "be thorough and
  explain fully" in another).
- **Testing only single-turn, happy-path, as yourself.**
- **Assuming a prompt transfers** across models or model versions without re-testing.
- **Optimizing for vibes in the playground** instead of against a case set.

## Exercises

1. **Deconstruct an LLM-written prompt.** Take one that disappointed you. Mark every line as
   (a) something the model would do anyway, (b) a rule without a reason, (c) a contradiction,
   or (d) actually useful context. Rewrite it with only (d), plus reasons for the rules that matter.
2. **User simulator.** Write three user personas (confused novice, impatient expert,
   adversarial). Have a model play each against your bot for 8 turns. Read all transcripts.
   Error-analyze.
3. **Evidence-driven revision.** Use the meta-prompt above with 10 real failures. Run the old
   and new prompts over 30 cases × 3 trials each. Report pass^3.
4. **(Stretch) GEPA.** If you have 50+ labeled cases and a metric, try GEPA via DSPy on one
   prompt. Compare against your best manual revision.

## Sources

- OpenAI: [GPT-5 prompting guide](https://developers.openai.com/cookbook/examples/gpt-5/gpt-5_prompting_guide) (contradictory instructions), [GPT-5.2 prompting guide](https://cookbook.openai.com/examples/gpt-5/gpt-5-2_prompting_guide), [Latest-model guidance](https://developers.openai.com/api/docs/guides/latest-model)
- Coverage of OpenAI's 2026 lean-prompt guidance: [Stop Over-Prompting (Decrypt / Yahoo Tech)](https://tech.yahoo.com/ai/chatgpt/articles/stop-over-prompting-openai-gpt-224604153.html)
- OpenAI: [Prompt optimization cookbook](https://github.com/openai/openai-cookbook/blob/main/examples/gpt-5/prompt-optimization-cookbook.ipynb)
- Anthropic: [Prompting best practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)
- Agrawal et al.: [GEPA: Reflective Prompt Evolution Can Outperform Reinforcement Learning (ICLR 2026)](https://arxiv.org/abs/2507.19457), [gepa-ai/gepa](https://github.com/gepa-ai/gepa)
- Decagon: [Optimizing GEPA for production](https://decagon.ai/blog/optimizing-gepa-for-production)
- MIT News: [Personalization features can make LLMs more agreeable (2026)](https://news.mit.edu/2026/personalization-features-can-make-llms-more-agreeable-0218)
- [Does Sycophancy Change Decisions? (CHI 2026)](https://dl.acm.org/doi/10.1145/3772318.3790934)
- Anthropic: [Demystifying evals for AI agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents), on pass^k for user-facing consistency
