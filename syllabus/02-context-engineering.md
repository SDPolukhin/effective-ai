# Module 2: Context engineering

## Why it matters

Anthropic defines context engineering as *"curating and maintaining the optimal set of
tokens during inference."* That includes the system prompt, tools, examples, message
history, retrieved data, and tool outputs. In agentic work, the prompt you type is a small
fraction of what the model sees. The rest is context you either engineer or leave to
accumulate by accident.

Prompt engineering asks *"how do I phrase this?"* Context engineering asks *"what should
the model be looking at right now, and what should it not?"*

## Core ideas

### 1. The goal is the smallest high-signal context

Because of context rot (Module 0), the target is not "everything that might be relevant."
It's the **smallest set of high-signal tokens that makes the desired outcome likely**.
Chroma found every tested model did much better on focused ~300-token inputs than on the
same question buried in ~113k tokens of mostly irrelevant history. Liu et al.'s earlier
"Lost in the Middle" (2023) showed information in the middle of long contexts gets
under-used.

### 2. Four operations: Write, Select, Compress, Isolate

This taxonomy (popularized by LangChain) is a good checklist:

| Operation | Meaning | Examples |
|---|---|---|
| **Write** | Persist information *outside* the window for later | Progress notes, `PLAN.md`, `tests.json`, git commits, memory files |
| **Select** | Pull in only what's needed, when it's needed | grep/glob instead of dumping files; skills loaded on demand; RAG |
| **Compress** | Shrink what must stay | Compaction and summaries; trimming tool outputs; clearing old tool results |
| **Isolate** | Keep separate concerns in separate windows | Subagents for exploration; fresh sessions per task; sandboxed code execution |

A rule of thumb from practitioners: **select before you compress**. Choose the right
sources before you worry about shrinking them.

### 3. Just-in-time beats just-in-case

Agents like Claude Code don't pre-load the codebase. They hold lightweight references
(paths, queries, URLs) and load content with tools when needed. You can apply the same idea
to your own prompts: **point to sources** ("look at how `HotDogWidget.php` does it", "check
the git history of `ExecutionFactory`") instead of pasting everything.

### 4. Always-loaded context must earn its place

Files loaded every session (CLAUDE.md, AGENTS.md, system prompts, tool definitions) are a
**tax on every turn**. The evidence is sobering:

- **ETH Zürich (2026), "Evaluating AGENTS.md":** Across multiple agents and models,
  repository context files **did not generally improve task success**, and **raised
  inference cost by over 20%**. This held for both LLM-generated and developer-written
  files. Repository overviews, despite being widely recommended, didn't help. Unnecessary
  requirements made tasks *harder*. The authors recommend describing only **minimal
  requirements**.
- Anthropic's own guidance agrees: *"For each line, ask: would removing this cause Claude
  to make mistakes? If not, cut it. Bloated CLAUDE.md files cause Claude to ignore your
  actual instructions."*

**What belongs in always-loaded context:**
- commands the model can't guess (build, test, lint, dev env quirks)
- conventions that *differ* from defaults
- non-obvious gotchas and architectural decisions
- repository etiquette (branching, PR conventions)

**What doesn't:**
- file-by-file tours
- standard language conventions
- "write clean code"
- long tutorials or API docs (link them instead)
- anything that changes frequently

### 5. Progressive disclosure: skills and on-demand knowledge

"Agent Skills" (introduced by Anthropic in October 2025 and since adopted by other harnesses)
are the canonical pattern here. Only a short **name + description** sits in context. The full
instructions load when the task matches. Use this pattern for domain knowledge and workflows
that are only *sometimes* relevant, and keep the always-on file for things that are *always*
relevant.

### 6. Tools are context too

Tool definitions and tool outputs are often the biggest consumers of context:

- **Scope tools per task or agent.** Loading every MCP server into every session is a known
  anti-pattern. Use tool search or deferred loading where the harness supports it.
- **Prefer CLIs** (`gh`, `aws`, `kubectl`) where available. They're context-efficient and
  models know them well.
- **Filter data before it reaches the model.** Anthropic's "code execution with MCP" post
  describes agents writing code to call tools and filter results in a sandbox. In their
  example, token usage dropped from ~150k to ~2k.
- If you write tools, make outputs **concise and high-signal**: paginate, truncate with
  hints, return IDs *and* human-readable names, and write actionable error messages. See
  Anthropic's "Writing effective tools for agents."

### 7. Long tasks: compaction vs fresh start

When a session gets long, you have two choices:

- **Compaction**: summarize history in place. It's continuous but lossy, and the summary
  can drop the one detail that mattered.
- **Reset + handoff artifact**: start fresh from written state (progress file, plan, git
  log). It's clean, but only as good as the handoff.

Anthropic's long-running-agent work leans on **structured handoff artifacts**: a JSON
feature list with pass/fail status, a free-text progress log, and git as the source of
truth. Their docs note newer models are *"extremely effective at discovering state from the
local filesystem,"* so a fresh window with good notes often beats a compacted one.

## Techniques

**In interactive sessions (Claude Code or similar):**
- `/clear` between unrelated tasks. After two failed corrections on the same issue, clear
  and restart with a better prompt that includes what you learned.
- Delegate broad exploration to a subagent ("use a subagent to investigate how token
  refresh works"). You get the summary, not 40 file reads.
- Steer compaction: `/compact focus on the API changes`, or add standing instructions
  like "when compacting, preserve the list of modified files and test commands."
- Use side-channel questions (for example, `/btw`) for things that shouldn't stay in history.

**In your own harnesses and apps:**
- Keep the system prompt stable and cacheable. Put volatile material later. Cache hit rate is
  now a first-class metric: it's cheaper *and* usually indicates a disciplined context layout.
- Clear or truncate stale tool results once they've been acted on.
- Give the agent a scratch file for notes rather than relying on the transcript.

## Anti-patterns

- **The kitchen-sink session.** Three unrelated tasks in one conversation.
- **The /init-and-forget CLAUDE.md.** An auto-generated overview that the ETH study suggests
  costs you 20% more tokens for no gain.
- **Instruction whack-a-mole.** Adding a line to CLAUDE.md after every mistake. If the model
  already does something right without the line, delete the line. If a rule *must* hold
  every time, make it a **hook** (deterministic), not an instruction (advisory).
- **Pasting whole files "for context"** when a path and a pointer would do.
- **Loading 15 MCP servers** "just in case."

## Exercises

1. **CLAUDE.md / AGENTS.md diet.** For each line in your context file, ask whether removing it
   would cause a mistake. Cut everything else. Run your next five tasks and note whether anything
   broke. (The ETH result predicts it probably won't.)
2. **Context budget.** In your agent tool, check what's loaded at session start (for example,
   `/context` in Claude Code). How many tokens go to tools and memory before you type anything?
3. **Handoff drill.** On a multi-hour task, stop halfway. Have the agent write `PROGRESS.md`
   (what's done, what's next, gotchas). Start a brand-new session from only that file and the
   repo. Did it pick up cleanly? What was missing?
4. **Skill extraction.** Find one instruction block that's only relevant to one kind of task
   (for example, "how we write migrations"). Move it into a skill and out of the always-on file.

## Sources

- Anthropic: [Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- Anthropic: [Equipping agents for the real world with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)
- Anthropic: [Code execution with MCP](https://www.anthropic.com/engineering/code-execution-with-mcp)
- Anthropic: [Writing effective tools for agents — with agents](https://www.anthropic.com/engineering/writing-tools-for-agents)
- Anthropic: [Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- Anthropic: [Best practices for Claude Code](https://code.claude.com/docs/en/best-practices), covering CLAUDE.md include/exclude, context management, and subagents
- Gloaguen et al., ETH Zürich (2026): [Evaluating AGENTS.md: Are Repository-Level Context Files Helpful for Coding Agents?](https://arxiv.org/abs/2602.11988)
- Chroma: [Context Rot](https://www.trychroma.com/research/context-rot)
- Liu et al. (2023): [Lost in the Middle: How Language Models Use Long Contexts](https://arxiv.org/abs/2307.03172)
- Sourcegraph: [Context Engineering: A Practical Guide for AI Agents (2026)](https://sourcegraph.com/blog/context-engineering)
