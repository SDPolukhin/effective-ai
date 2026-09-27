# Sources

All sources cited across the syllabus, grouped by module (retrieved September 2026). Primary sources such as lab docs, engineering posts, and papers are preferred. Secondary coverage is used only where the primary source wasn't reachable.

## [Module 0: A working mental model](syllabus/00-mental-model.md)

- Chroma: [Context Rot: How Increasing Input Tokens Impacts LLM Performance](https://www.trychroma.com/research/context-rot)
- Anthropic: [Best practices for Claude Code](https://code.claude.com/docs/en/best-practices)
- Anthropic: [Harness design for long-running application development](https://www.anthropic.com/engineering/harness-design-long-running-apps)
- Anthropic: [Prompting best practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)
- METR: [Measuring the impact of early-2025 AI on experienced OS developer productivity](https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/)
- METR: [We are changing our developer productivity experiment design (Feb 2026)](https://metr.org/blog/2026-02-24-uplift-update/)
- Stack Overflow: [2025 Developer Survey](https://survey.stackoverflow.co/2025/), [Closing the developer AI trust gap](https://stackoverflow.blog/2026/02/18/closing-the-developer-ai-trust-gap/)
- Wharton GAIL: [Prompting Science Report 2: The Decreasing Value of Chain of Thought](https://gail.wharton.upenn.edu/research-and-insights/tech-report-chain-of-thought/), [Report 4: Expert Personas Don't Improve Factual Accuracy](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=5879722)

## [Module 1: Prompting fundamentals, evidence vs folklore](syllabus/01-prompting-fundamentals.md)

- Anthropic: [Prompting best practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices), covering clarity, context and motivation, examples, XML structure, long-context placement, overthinking, and migration
- Anthropic: [Best practices for Claude Code](https://code.claude.com/docs/en/best-practices), with before/after prompt tables
- OpenAI: [GPT-5.2 prompting guide](https://cookbook.openai.com/examples/gpt-5/gpt-5-2_prompting_guide) and [prompt-optimization cookbook](https://github.com/openai/openai-cookbook/blob/main/examples/gpt-5/prompt-optimization-cookbook.ipynb)
- Wharton Generative AI Labs, Prompting Science Reports: [Report 2 (CoT)](https://gail.wharton.upenn.edu/research-and-insights/tech-report-chain-of-thought/), [Report 3 (tips/threats)](https://arxiv.org/abs/2508.00614), [Report 4 (personas)](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=5879722)
- Sclar et al. (2023): [Quantifying Language Models' Sensitivity to Spurious Features in Prompt Design](https://arxiv.org/abs/2310.11324)

## [Module 2: Context engineering](syllabus/02-context-engineering.md)

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

## [Module 3: Verification and evals, making "good" measurable](syllabus/03-verification-and-evals.md)

- Anthropic: [Demystifying evals for AI agents (Jan 2026)](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)
- Anthropic: [Quantifying infrastructure noise in agentic coding evals (Feb 2026)](https://www.anthropic.com/engineering)
- Anthropic: [Best practices for Claude Code, "Give Claude a way to verify its work"](https://code.claude.com/docs/en/best-practices)
- Anthropic: [Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- Hamel Husain: [AI Evals: Everything You Need to Know (FAQ)](https://hamel.dev/blog/posts/evals-faq/)
- Hamel Husain: [Using LLM-as-a-Judge for Evaluation: A Complete Guide](https://hamel.dev/blog/posts/llm-judge/)
- Lenny's Newsletter: [Evals, error analysis, and better prompts (Hamel Husain)](https://www.lennysnewsletter.com/p/evals-error-analysis-and-better-prompts)
- Pragmatic Engineer: [A pragmatic guide to LLM evals for devs](https://newsletter.pragmaticengineer.com/p/evals)
- [How to Correctly Report LLM-as-a-Judge Evaluations (2025)](https://arxiv.org/abs/2511.21140)

## [Module 4: Agentic coding workflows, delegating without micromanaging](syllabus/04-agentic-coding.md)

- Anthropic: [Best practices for Claude Code](https://code.claude.com/docs/en/best-practices), covering explore-plan-code, interview-to-spec, hooks vs CLAUDE.md, course correction, fan-out, and failure patterns
- Anthropic: [Prompting best practices, "Agentic systems"](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices), covering over-eagerness, test-gaming, hallucination, and multi-window workflows
- Anthropic: [Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- Anthropic: [Harness design for long-running application development (Mar 2026)](https://www.anthropic.com/engineering/harness-design-long-running-apps)
- Anthropic: [2026 Agentic Coding Trends Report, summary](https://tessl.io/blog/8-trends-shaping-software-engineering-in-2026-according-to-anthropics-agentic-coding-report)
- Microsoft: [Spec-Driven Development: A Spec-First Approach to AI-Native Engineering](https://developer.microsoft.com/blog/spec-driven-development-ai-native-engineering/)
- OpenAI: [Codex prompting guide](https://github.com/openai/openai-cookbook/blob/main/examples/gpt-5/codex_prompting_guide.ipynb)
- Simon Willison: [Live blog, Code w/ Claude 2026](https://simonwillison.net/2026/May/6/code-w-claude-2026/)

## [Module 5: Multi-agent systems and reviewer agents](syllabus/05-multi-agent-and-review.md)

- Anthropic: [Building effective agents (Dec 2024)](https://www.anthropic.com/research/building-effective-agents)
- Anthropic: [How we built our multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system)
- Anthropic: [Building a C compiler with a team of parallel Claudes (Feb 2026)](https://www.anthropic.com/engineering/building-c-compiler)
- Anthropic: [Harness design for long-running application development (Mar 2026)](https://www.anthropic.com/engineering/harness-design-long-running-apps)
- Anthropic: [Best practices for Claude Code, "Add an adversarial review step"](https://code.claude.com/docs/en/best-practices)
- Cognition: [Don't Build Multi-Agents (2025)](https://cognition.com/blog/dont-build-multi-agents)
- Cognition: [Multi-Agents: What's Actually Working (2026)](https://cognition.com/blog/multi-agents-working)
- [Cross-Context Review: Separating Production and Review Sessions (2026)](https://arxiv.org/html/2603.12123)
- [Cross-Model LLM Code Review: Should you use Claude to review Codex or vice versa? (2026)](https://arxiv.org/html/2607.21656v1)

## [Module 6: RAG and retrieval](syllabus/06-rag-and-retrieval.md)

- Anthropic: [Introducing Contextual Retrieval](https://www.anthropic.com/engineering/contextual-retrieval)
- Anthropic: [How we built our multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system), on search strategy
- Anthropic: [Prompting best practices, long-context prompting](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)
- Hamel Husain: [RAG series, including P6: Context Rot](https://hamel.dev/notes/llm/rag/p6-context_rot.html)
- [Hybrid Search: BM25, Vector & Reranking Reference 2026](https://www.digitalapplied.com/blog/hybrid-search-bm25-vector-reranking-reference-2026)
- [Beyond Semantic Similarity: Rethinking Retrieval for Agentic Search via Direct Corpus Interaction (2026)](https://arxiv.org/abs/2605.05242)
- [Question's Gambit: The First Move Matters in Agentic Deep Search (2026)](https://arxiv.org/abs/2609.14412)
- Microsoft Research: [GraphRAG](https://microsoft.github.io/graphrag/)
- Chroma: [Context Rot](https://www.trychroma.com/research/context-rot)

## [Module 7: Prompts for user-facing products](syllabus/07-user-facing-prompts.md)

- OpenAI: [GPT-5 prompting guide](https://developers.openai.com/cookbook/examples/gpt-5/gpt-5_prompting_guide) (contradictory instructions), [GPT-5.2 prompting guide](https://cookbook.openai.com/examples/gpt-5/gpt-5-2_prompting_guide), [Latest-model guidance](https://developers.openai.com/api/docs/guides/latest-model)
- Coverage of OpenAI's 2026 lean-prompt guidance: [Stop Over-Prompting (Decrypt / Yahoo Tech)](https://tech.yahoo.com/ai/chatgpt/articles/stop-over-prompting-openai-gpt-224604153.html)
- OpenAI: [Prompt optimization cookbook](https://github.com/openai/openai-cookbook/blob/main/examples/gpt-5/prompt-optimization-cookbook.ipynb)
- Anthropic: [Prompting best practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)
- Agrawal et al.: [GEPA: Reflective Prompt Evolution Can Outperform Reinforcement Learning (ICLR 2026)](https://arxiv.org/abs/2507.19457), [gepa-ai/gepa](https://github.com/gepa-ai/gepa)
- Decagon: [Optimizing GEPA for production](https://decagon.ai/blog/optimizing-gepa-for-production)
- MIT News: [Personalization features can make LLMs more agreeable (2026)](https://news.mit.edu/2026/personalization-features-can-make-llms-more-agreeable-0218)
- [Does Sycophancy Change Decisions? (CHI 2026)](https://dl.acm.org/doi/10.1145/3772318.3790934)
- Anthropic: [Demystifying evals for AI agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents), on pass^k for user-facing consistency

## [Module 8: Failure modes and security](syllabus/08-failure-modes-and-security.md)

- Simon Willison: [The lethal trifecta for AI agents](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/)
- [The Promptware Kill Chain (2026)](https://arxiv.org/abs/2601.09625)
- Anthropic: [Prompting best practices, "Balancing autonomy and safety," "Avoid focusing on passing tests"](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)
- Anthropic: [How we built Claude Code auto mode (Mar 2026)](https://www.anthropic.com/engineering), [Beyond permission prompts (Oct 2025)](https://www.anthropic.com/engineering)
- Anthropic: [Eval awareness in BrowseComp performance (Mar 2026)](https://www.anthropic.com/engineering)
- MIT News: [Personalization features can make LLMs more agreeable](https://news.mit.edu/2026/personalization-features-can-make-llms-more-agreeable-0218)
- [Does Sycophancy Change Decisions? (CHI 2026)](https://dl.acm.org/doi/10.1145/3772318.3790934)
- [What Counts as AI Sycophancy? A Taxonomy and Expert Survey (2026)](https://arxiv.org/html/2605.21778v1)

## [Module 9: A four-week practice plan](syllabus/09-practice-plan.md)

See that module's "Staying current" list for ongoing sources.
