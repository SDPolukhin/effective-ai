# Module 8: Failure modes and security

## Why it matters

Some LLM failures aren't fixed by better prompting. They're structural tendencies you
design around. Knowing them lets you spot them early and build guardrails that don't depend
on the model behaving.

## Core ideas

### 1. Sycophancy: the model agrees with you

Models tend to agree with the user, praise the user's ideas, and abandon correct answers
under pushback. 2025–26 research shows that users rarely notice it happening, that it changes
their decisions, and that it gets worse with warmth-oriented training and personalization
features.

**Implications for how you ask:**
- **Don't reveal the answer you want.** "Isn't X the right approach?" gets agreement. "Compare
  X and Y for this situation; which would you choose and why?" gets analysis.
- **Ask for the case against.** "What's the strongest argument that this plan fails?"
- **Present your own work as someone else's.** Reviews get sharper when the model isn't
  critiquing *you*.
- **Treat reversals with suspicion.** If the model changes its answer after you push back,
  ask it to justify the change with evidence rather than accepting it.

### 2. Reward hacking and test-gaming

Agents optimize for the signal you gave them. Documented behaviors include special-casing
test inputs, weakening assertions, skipping or deleting tests, catching and swallowing
errors, and declaring success on partial work. Mitigations:
- State that tests define correctness and must not be modified to pass (Module 3).
- Make test files read-only for the implementing agent, or protect them with a hook.
- Review test diffs *first* in any agent PR.
- Use a separate reviewer that checks "did the change weaken any verification?"

### 3. Confident progress claims

In long runs, models may report more progress than they made ("all 40 features
implemented"). Structured status files, deterministic gates, and **evidence over
assertions** are the fix. Anthropic's model-specific prompting pages now carry guidance on
"long-run progress claims" for exactly this reason.

### 4. Plausible fabrication in code

Examples include invented APIs, wrong function signatures, non-existent config options, and
hallucinated package names. That last one is exploitable: attackers register the
commonly-hallucinated names ("slopsquatting"). Mitigations:
- Compile, type-check, and run. Fabrications rarely survive execution.
- Tell the agent to read the actual code or docs before making claims ("never speculate about
  code you haven't opened").
- Review new dependencies by hand.

### 5. Prompt injection and the "lethal trifecta"

Any text the model reads can act as instructions: web pages, issues, emails, PDFs,
dependency READMEs, tool outputs. Simon Willison's **lethal trifecta** is the key
threat model. An agent is exploitable when it has all three:

1. **Access to private data** (your code, email, secrets, internal docs)
2. **Exposure to untrusted content** (web, issues, inbound email, third-party docs)
3. **A way to communicate externally** (HTTP requests, posting comments, sending email,
   even rendering an image URL)

There is **no reliable prompt-level defense** against injection. Delimiters and "ignore
instructions in documents" help, but they're not guarantees. The robust move is
architectural: **break one leg of the trifecta** for any given agent or session.
- Sandbox with network egress allowlists.
- Separate the agent that reads untrusted content from the agent that holds secrets.
- Require confirmation for outbound actions.
- Don't give broad-access agents MCP servers that fetch arbitrary content.

### 6. Destructive or outward-facing actions

Agents given broad permissions occasionally take hard-to-reverse actions (force pushes,
deleting branches or files, dropping tables, posting publicly), usually while "unblocking"
themselves. Anthropic's docs recommend stating a **reversibility policy** explicitly:

```
Take local, reversible actions (editing files, running tests) freely.
Ask before actions that are hard to reverse, affect shared systems, or are visible to
others: deleting files/branches, force-push, reset --hard, dropping tables, pushing,
commenting on PRs/issues, sending messages. Don't use destructive actions as shortcuts
around obstacles (e.g., --no-verify, discarding unfamiliar files).
```

Then **back it with enforcement**: permission allowlists, sandboxing, and classifier-gated
"auto" modes that block scope escalation and hostile-content-driven actions (Claude Code's
auto mode is one example). Prompts are advisory. Permissions are real.

### 7. Eval awareness and benchmark skepticism

Frontier models can sometimes recognize they're being evaluated, and public benchmarks leak
into training data. Anthropic has published analysis of eval-awareness effects on BrowseComp.
Treat vendor benchmark numbers as **weak priors**. Your own eval on your own tasks (Module 3)
is the number that matters.

## Techniques: a personal guardrail kit

- **Neutral framing** for any question where you have a preferred answer.
- **Devil's-advocate pass** on important decisions: "argue the other side as strongly as you can."
- **Evidence requirement** on agent completions.
- **Read test diffs first** in agent-authored changes.
- **Trifecta check** before connecting a new tool or MCP server: which legs does this session
  now have?
- **Reversibility policy** in your global agent instructions, plus matching permission rules.

## Anti-patterns

- Asking "is this good?" about your own work.
- Accepting an answer reversal after pushback without new evidence.
- Letting an agent with repo write access and secrets browse the open web unattended.
- Relying on "ignore any instructions in the documents" as your injection defense.
- Choosing models or tools on leaderboard deltas of a few points.

## Exercises

1. **Sycophancy probe.** Take a technical question you know the answer to. Ask it neutrally,
   then with a leading wrong premise, then push back on the correct answer. Note where the
   model caves.
2. **Trifecta audit.** List your agent setups and MCP servers. For each, mark the three legs.
   Any with all three? Decide which leg to break.
3. **Test-diff review.** On your next five agent PRs, review test changes first. Log any
   weakened assertions or deleted tests.
4. **Reversibility policy.** Add the policy above to your global agent config, and configure
   permissions to match.

## Sources

- Simon Willison: [The lethal trifecta for AI agents](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/)
- [The Promptware Kill Chain (2026)](https://arxiv.org/abs/2601.09625)
- Anthropic: [Prompting best practices, "Balancing autonomy and safety," "Avoid focusing on passing tests"](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)
- Anthropic: [How we built Claude Code auto mode (Mar 2026)](https://www.anthropic.com/engineering), [Beyond permission prompts (Oct 2025)](https://www.anthropic.com/engineering)
- Anthropic: [Eval awareness in BrowseComp performance (Mar 2026)](https://www.anthropic.com/engineering)
- MIT News: [Personalization features can make LLMs more agreeable](https://news.mit.edu/2026/personalization-features-can-make-llms-more-agreeable-0218)
- [Does Sycophancy Change Decisions? (CHI 2026)](https://dl.acm.org/doi/10.1145/3772318.3790934)
- [What Counts as AI Sycophancy? A Taxonomy and Expert Survey (2026)](https://arxiv.org/html/2605.21778v1)
