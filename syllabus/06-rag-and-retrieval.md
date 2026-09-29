# Module 6: RAG and retrieval

## Why it matters

Most "the model made it up" failures are really retrieval failures: the right information
never reached the context window, or arrived buried among distractors. RAG is context
engineering (Module 2) for knowledge that doesn't fit, or shouldn't all be loaded at once.

## Core ideas

### 1. First decide whether you need retrieval at all

| Situation | Best approach |
|---|---|
| Knowledge fits comfortably (tens of thousands of tokens) and is stable | **Put it in the context** with prompt caching. Simpler and often better. |
| Large or changing corpus of documents | **Retrieval pipeline** (hybrid search + reranking) |
| Codebases and structured file trees | **Agentic search**: let the agent grep, glob, read, and use LSP/symbol tools |
| Multi-hop questions over large corpora | **Agentic RAG**: retrieval as a tool the model calls repeatedly |
| Questions about relationships or corpus-wide themes | Consider graph-based approaches (GraphRAG), with care for cost |

Anthropic's Claude Code team has said publicly that early versions used a local vector index,
and that **agentic search (grep/glob/read) worked better** for code. Code has exact
identifiers, and embeddings are fuzzy. Research in 2026 continues to explore "direct corpus
interaction" as an alternative to pure embedding similarity.

Remember context rot, though. "Just put it all in the context" has a ceiling well below the
advertised window.

### 2. The 2026 default retrieval stack

For a document corpus, practitioners have largely converged on:

1. **Sensible chunking.** Split on structure (headings, sections), not fixed character counts.
   Keep chunks self-contained.
2. **Contextual chunk enrichment.** Anthropic's *Contextual Retrieval* prepends a short,
   LLM-generated description situating each chunk in its document before indexing. Their
   reported top-20 retrieval failure rates:
   - baseline: 5.7%
   - contextual embeddings: 3.7% (−35%)
   - \+ contextual BM25: 2.9% (−49%)
   - \+ reranking: **1.9% (−67%)**
3. **Hybrid search.** Combine BM25 (exact terms: IDs, error codes, names) with dense
   embeddings (meaning), fused with Reciprocal Rank Fusion. This beats either alone on most
   public benchmarks.
4. **Cross-encoder reranking** of the top ~50–150 candidates down to the handful you pass to
   the model.
5. **Metadata filters** (date, product, permission scope) *before* similarity, where possible.

### 3. Agentic RAG: retrieval inside the loop

Instead of retrieve-once-then-answer, give the model a search tool and let it decide what to
look up, rewrite queries, follow references, and stop when it has enough. It's better for
multi-hop questions and worse for latency and cost. The first query matters: 2026 work on
agentic deep search finds the opening move strongly shapes the outcome. Anthropic's
research-agent guidance: *"start with short, broad queries, evaluate what's available, then
progressively narrow."*

### 4. Grounding the generation step

Retrieval gets the right text in. Then the model has to actually *use* it:
- Put retrieved documents **above** the question, wrapped in tagged blocks with source
  metadata (Module 1).
- Ask for **quotes first, then the answer**, or require citations per claim.
- **Explicitly allow "not found in the provided sources."** Without this, the model fills
  gaps from training data.
- For high-stakes answers, add a verification pass: check each claim against its cited
  passage.

### 5. Evaluate retrieval and generation separately

This is the most common RAG mistake. When an answer is wrong, you need to know whether
**retrieval missed** or **generation mis-used** what it got.

- **Retrieval evals:** a labeled set of (question → relevant chunk IDs). Measure recall@k and
  MRR. This is cheap, deterministic, and fast to iterate.
- **Generation evals:** given the *correct* context, is the answer faithful and complete?
  Use binary judges per criterion (Module 3).
- **End-to-end:** a small set of real user questions with human-verified answers.

## Techniques: when using AI tools as a user (not building RAG)

Even without building a pipeline, the same principles apply in chat tools, project
knowledge, and NotebookLM-style tools:
- **Curate what you upload.** Five relevant documents beat fifty tangential ones.
- **Ask for quotes and locations** ("quote the passage and give the section").
- **Tell it what to do when the source is silent.**
- **Break multi-document questions into steps.** First extract relevant facts per document,
  then synthesize.
- **Verify numbers and names** against the source. Those are where fluent errors hide.

## Anti-patterns

- **Embedding-only search** over corpora full of identifiers, codes, and names.
- **Fixed-size chunking** that splits tables, code, and definitions mid-thought.
- **Top-k = 50 "to be safe."** That injects distractors and triggers context rot.
- **Tuning the prompt when retrieval is the problem.** Check recall first.
- **No "I don't know" path.**
- **GraphRAG by default.** It's expensive to build and maintain. Use it when relationship-heavy
  queries are measured to fail with hybrid search.

## Exercises

1. **Retrieval-only eval.** Use a RAG system you already run, or build a minimal one over a
   corpus you know well (a project's docs, your notes, or this syllabus): chunk it, embed it,
   and retrieve the top 5. Write 20 questions and label which documents should be retrieved.
   Measure recall@5. Is retrieval your bottleneck?
2. **Hybrid A/B.** If your system uses pure vector search, add BM25 with RRF and compare
   recall@5 on the same 20 questions.
3. **Grounding prompt.** Rewrite a Q&A prompt to (a) put documents first, (b) require quotes,
   and (c) allow "not found." Compare hallucination rates on questions deliberately *not*
   answerable from the sources.
4. **Code search reflection.** Next time an agent misunderstands your codebase, check which
   files it actually read. Was it a search failure or a reasoning failure?

## Sources

- Anthropic: [Introducing Contextual Retrieval](https://www.anthropic.com/engineering/contextual-retrieval)
- Anthropic: [How we built our multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system), on search strategy
- Anthropic: [Prompting best practices, long-context prompting](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)
- Hamel Husain: [RAG series, including P6: Context Rot](https://hamel.dev/notes/llm/rag/p6-context_rot.html)
- [Hybrid Search: BM25, Vector & Reranking Reference 2026](https://www.digitalapplied.com/blog/hybrid-search-bm25-vector-reranking-reference-2026)
- [Beyond Semantic Similarity: Rethinking Retrieval for Agentic Search via Direct Corpus Interaction (2026)](https://arxiv.org/abs/2605.05242)
- [Question's Gambit: The First Move Matters in Agentic Deep Search (2026)](https://arxiv.org/abs/2609.14412)
- Microsoft Research: [GraphRAG](https://microsoft.github.io/graphrag/)
- Chroma: [Context Rot](https://www.trychroma.com/research/context-rot)
