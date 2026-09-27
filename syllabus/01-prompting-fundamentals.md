# Module 1: Prompting fundamentals, evidence vs folklore

## Why it matters

"Prompt engineering" has a reputation as unmeasurable craft. It's partly craft, but
a small set of techniques is consistently supported by lab guidance and research. Another
set is folklore that has stopped working, or never did. Knowing which is which saves you
from cargo-culting.

## Core ideas

### What reliably works

**1. Say what you want, explicitly, including the level of ambition.**
Modern models follow instructions literally and don't infer unstated requirements. If you
want a fully-featured result, say so. If you want a minimal diff, say that instead. Ask for
*action* ("change this function") when you want action, and *advice* ("what would you
change?") when you want advice.

**2. Explain *why*, not just *what*.**
Anthropic's example: instead of `NEVER use ellipses`, write *"Your response will be read aloud
by a text-to-speech engine, so never use ellipses since the engine won't know how to
pronounce them."* The model generalizes from the reason and handles cases your rule didn't
anticipate. This is the single highest-leverage habit for system prompts (Module 7).

**3. Examples (few-shot) are the most reliable way to control format, tone, and structure.**
Use 3–5 examples that are **relevant**, **diverse** (so the model doesn't copy incidental
features), and **clearly delimited** (for example, `<example>` tags). One example gets
copied. Several varied examples get generalized.

**4. Separate instructions from data structurally.**
Use XML-style tags or clear headers to separate `<instructions>`, `<context>`, `<documents>`,
and `<input>`. This reduces misreads and gives you a basic defense against the model treating
pasted content as instructions (Module 8).

**5. For long inputs, put the documents first and the question last.**
With 20k+ tokens of material, put the documents at the top and the query at the end.
Anthropic reports up to 30% quality improvement on complex multi-document inputs. Asking the
model to first extract relevant quotes into tags, then answer, also helps it cut through
noise.

**6. Specify the output contract.**
Give the format, length, audience, and what to omit. Use structured outputs or a JSON schema
when a program consumes the result. Describe the format positively ("write in flowing prose
paragraphs") rather than only negatively ("no markdown"). Matching your prompt's own style
to the style you want back also helps.

**7. Define "done" and how to check it** (see Module 3). This belongs in almost every
non-trivial prompt.

### What's folklore (or has decayed)

| Technique | Status (2026) | Evidence |
|---|---|---|
| "Think step by step" | Small or no gain on reasoning models; costs time and tokens; can add variance | Wharton Prompting Science Report 2 |
| Expert personas ("You are a world-class…") for accuracy | Don't improve factual accuracy; low-knowledge personas can hurt; may help tone | Wharton Report 4 |
| Tipping or threatening the model | No reliable effect | Wharton Report 3 |
| ALL-CAPS "CRITICAL / MUST / NEVER" everywhere | Causes over-triggering on current models; if everything is emphasized, nothing is | Anthropic prompting docs; Claude Code docs |
| "Be thorough / if in doubt, use tool X" | Was needed for older, lazier models; now causes over-exploration and over-engineering | Anthropic migration guidance |
| Prefilling the assistant turn | No longer supported on recent Claude models; use structured outputs or instructions | Anthropic migration guidance |

Roles still have a place. A one-line role in a system prompt usefully sets *domain and
tone*. Just don't expect it to make the model more *correct*.

### Reasoning and "effort" are now settings, not prompt tricks

Current frontier models think adaptively. You control depth with an **effort / reasoning
level** parameter, not with "think harder" in the prompt. Two practical points:

- If a model over-thinks simple tasks, **lower the effort** before rewriting prompts. You
  can also tell it: *"choose an approach and commit to it; revisit only if new information
  contradicts it."*
- If it under-thinks hard tasks, raise effort, or ask it to reflect after tool results
  before acting ("interleaved thinking").

### Prompt sensitivity is real, so measure

Small formatting changes can swing results a lot. Sclar et al. (2023) found up to 76-point
accuracy swings from formatting alone on older open models. Frontier models are more robust,
but not immune. That's the core argument for Module 3: without an eval, you can't tell a real
improvement from noise.

## Techniques: a prompt skeleton

This skeleton works for most non-trivial one-off prompts (chat or agent):

```
<context>
What this is for, who it's for, and why it matters.
Relevant background the model can't infer. Where to look for more.
</context>

<task>
The concrete ask, phrased as an action.
Scope: what's in and what's explicitly out.
</task>

<constraints>
Only constraints that differ from sensible defaults, each with its reason.
</constraints>

<done_when>
Observable criteria. How to verify (command, test, check).
What evidence to show me.
</done_when>

<output>
Format, length, and audience.
</output>
```

You won't need every section every time. A two-line prompt with a clear `done_when` often
beats a page of instructions without one.

### Rewrite drills (before → after)

| Before | After |
|---|---|
| "Fix the login bug" | "Users report login fails after session timeout. Check the token refresh in `src/auth/`. Write a failing test that reproduces it, then fix it. Don't suppress errors; address the root cause." |
| "Add tests for foo.py" | "Add a test for `foo.py` covering the logged-out case. Avoid mocks; use the fixtures in `tests/conftest.py`." |
| "Make it better" | "Tighten this for a senior engineering audience: cut hedging, keep every technical claim, stay under 300 words." |
| "Summarize this" | "Summarize for a PM deciding whether to fund this. Lead with the decision-relevant risk. Quote the doc when stating numbers." |

## Anti-patterns

- **Rule accretion.** Adding a new `NEVER…` after every failure until the prompt is 3 pages of
  contradictory rules. Instead, find the root cause (missing context? missing example?) and
  **delete** rules that no longer change behavior.
- **Vague ambition.** "Make it production-ready" means different things to you and the model.
  Name the specific properties.
- **Asking the model to "not hallucinate."** That doesn't work. What works: give it the source,
  tell it to quote or cite, and allow "I don't know" explicitly.
- **Negative-only formatting.** "Don't use bullet points" works worse than describing what to
  do instead.

## Exercises

1. **Why-pass.** Take a system prompt or CLAUDE.md you use. For every rule that lacks a reason,
   add one clause of "because…". Delete any rule you can't justify.
2. **Example-pass.** Pick a task where format consistency matters. Write three diverse examples.
   Compare outputs with and without them across 10 inputs.
3. **Folklore A/B.** Pick a prompt with "You are an expert…" or "think step by step." Remove it.
   Run both versions on 10 inputs and compare blind. (This is a preview of Module 3.)

## Sources

- Anthropic: [Prompting best practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices), covering clarity, context and motivation, examples, XML structure, long-context placement, overthinking, and migration
- Anthropic: [Best practices for Claude Code](https://code.claude.com/docs/en/best-practices), with before/after prompt tables
- OpenAI: [GPT-5.2 prompting guide](https://cookbook.openai.com/examples/gpt-5/gpt-5-2_prompting_guide) and [prompt-optimization cookbook](https://github.com/openai/openai-cookbook/blob/main/examples/gpt-5/prompt-optimization-cookbook.ipynb)
- Wharton Generative AI Labs, Prompting Science Reports: [Report 2 (CoT)](https://gail.wharton.upenn.edu/research-and-insights/tech-report-chain-of-thought/), [Report 3 (tips/threats)](https://arxiv.org/abs/2508.00614), [Report 4 (personas)](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=5879722)
- Sclar et al. (2023): [Quantifying Language Models' Sensitivity to Spurious Features in Prompt Design](https://arxiv.org/abs/2310.11324)
