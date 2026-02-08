# AI Research Sync

**A model-agnostic instruction set that keeps any AI current with research published after its training cutoff.**

Not a script. Not a plugin. A plain-text instruction file that any LLM can execute.

---

## The Problem

Every AI model has a knowledge gap. Claude's training ended in May 2025. GPT-4's ended earlier. Local models vary. New research publishes daily — new architectures, scaling laws, reasoning breakthroughs, safety findings — and your model doesn't know about any of it.

You're working with an expert that stopped reading six months ago.

## The Solution

A markdown instruction file (`sync.md`) that tells any AI model to:

1. **Detect** its own training cutoff date
2. **Search** arXiv and other sources for significant papers published after that date
3. **Download** the PDFs to a local directory
4. **Track** what's been downloaded in a manifest file
5. **Stay incremental** — on subsequent runs, only grab what's new

No code dependencies. No API keys. No vendor lock-in. Just an instruction file that works with whatever model you're already using.

---

## How It Works

```
┌─────────────────────────────────────────────────────┐
│                     sync.md                          │
│            (instruction file — the brain)             │
│                                                       │
│  1. What is your training cutoff date?                │
│  2. What papers are already in the manifest?          │
│  3. Search for papers published after cutoff           │
│     that aren't in the manifest                       │
│  4. Download new papers                               │
│  5. Update the manifest                               │
│  6. (Optional) Generate summaries                     │
└───────────────────────┬─────────────────────────────┘
                        │
         ┌──────────────┼──────────────┐
         ▼              ▼              ▼
   ┌──────────┐  ┌──────────┐  ┌──────────┐
   │  Claude   │  │  Gemini  │  │  Ollama  │
   │  Opus 4.6 │  │  2.5     │  │  Local   │
   │  cutoff:  │  │  cutoff:  │  │  cutoff: │
   │  May 2025 │  │  varies   │  │  varies  │
   └──────────┘  └──────────┘  └──────────┘
         │              │              │
         ▼              ▼              ▼
   ┌──────────────────────────────────────┐
   │           papers/                     │
   │  arXiv_2501.12948_deepseek-r1.pdf    │
   │  arXiv_2412.19437_deepseek-v3.pdf    │
   │  arXiv_2507.06261_gemini-2.5.pdf     │
   │  ...                                  │
   │                                       │
   │           manifest.md                 │
   │  (tracks everything downloaded)       │
   └──────────────────────────────────────┘
```

The instruction file adapts to whichever model runs it. A model with a May 2025 cutoff pulls more papers than one with a January 2026 cutoff. Run it again next week, it only grabs the gap.

---

## Quick Start

### 1. Clone the repo

```bash
git clone https://github.com/jevierra/ai-research-sync.git
cd ai-research-sync
```

### 2. Create your papers directory

```bash
mkdir papers
```

### 3. Run the sync

Point your AI model at `sync.md` and tell it to execute. That's it.

**Claude Code:**
```
Read sync.md and execute the instructions.
```

**Any chat-based model:**
Copy the contents of `sync.md` into your conversation and tell the model to follow the instructions.

**Local model (Ollama):**
Works best with a wrapper script — see [docs/local-model-setup.md](docs/local-model-setup.md).

### 4. Check your results

```bash
ls papers/          # Downloaded PDFs
cat manifest.md     # What was downloaded and when
cat summaries.md    # Paper summaries (if generated)
```

### 5. Run again later

The manifest tracks everything. Next run only downloads new papers.

---

## Repository Structure

```
ai-research-sync/
├── README.md                          # You are here
├── sync.md                            # The core instruction file — run this
├── manifest-template.md               # Starter manifest (copy to manifest.md)
├── docs/
│   ├── how-it-works.md                # Detailed architecture explanation
│   ├── local-model-setup.md           # Using with Ollama/local models
│   ├── source-configuration.md        # Adding sources beyond arXiv
│   └── summary-format.md              # Paper summary specification
├── templates/
│   ├── manifest-template.md           # Blank manifest to start from
│   └── summary-template.md            # Summary format reference
├── examples/
│   ├── manifest-example.md            # What a populated manifest looks like
│   ├── summary-example.md             # Example paper summaries
│   └── first-run-output/              # Sample output from an initial sync
└── LICENSE
```

---

## What Gets Downloaded

**Default categories** (arXiv):
- `cs.LG` — Machine Learning
- `cs.AI` — Artificial Intelligence
- `cs.CL` — Computation and Language (NLP/LLMs)
- `cs.CV` — Computer Vision
- `stat.ML` — Machine Learning (Statistics)

**Priority topics:**
- Large language models and new model releases
- Reasoning, agents, and chain-of-thought
- Transformers and scaling laws
- Multimodal models
- Diffusion models
- Reinforcement learning (especially RLHF/RLAIF)
- Evaluation benchmarks
- Alignment and safety

**Sources:**
- arXiv (primary — free, open, no auth required)
- HuggingFace Daily Papers (curated, high-signal)
- Papers With Code (ties research to implementations)
- Semantic Scholar (citation data for ranking)

---

## The Manifest

The manifest is the memory. It tracks every paper downloaded so subsequent runs know what's already present.

```markdown
# AI Research Sync — Manifest

**Last sync:** 2026-02-06
**Model used:** Claude Opus 4.6
**Training cutoff:** 2025-05-01
**Papers downloaded:** 19

| arXiv ID | Title | Date | Downloaded | Source |
|----------|-------|------|------------|--------|
| 2501.12948 | DeepSeek-R1 | 2025-01-20 | 2026-02-06 | arXiv |
| 2412.19437 | DeepSeek-V3 | 2024-12-27 | 2026-02-06 | arXiv |
| ... | ... | ... | ... | ... |
```

---

## Incremental Updates

**First run:** Full backfill from training cutoff to today. Downloads everything.

**Subsequent runs:** Reads the manifest, identifies the gap between last sync date and today, downloads only new papers.

**Switching models:** If you switch from Claude (May 2025 cutoff) to a local model (earlier cutoff), the sync detects the wider gap and backfills what's missing.

The manifest is the single source of truth. No duplicate downloads. No missed papers.

---

## Summary Generation (Optional)

After downloading, the instruction file can generate summaries for each paper:

```markdown
### DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via RL
**arXiv:** 2501.12948
**Date:** 2025-01-20
**Priority:** URGENT

**Summary:** Demonstrated that o1-level reasoning emerges from pure RL (GRPO)
without supervised fine-tuning on reasoning traces. Open-sourced R1 and distilled
variants, democratizing reasoning models.

**Key Insights:**
- Pure RL can produce chain-of-thought reasoning without CoT training data
- Distilled 7B variants retain significant reasoning capability

**Why It Matters:** Broke the assumption that reasoning requires proprietary
training pipelines. Every lab can now build reasoning models.
```

Summary quality depends on the model. Frontier models (Claude, GPT) produce better summaries. Local models produce adequate ones. Both are useful.

---

## Design Principles

1. **Plain text is universal.** Markdown files work with every model, every platform, every tool. No dependencies to break.

2. **Model-agnostic by design.** The same instruction file works whether you're running Opus 4.6, Gemini 2.5, or a 7B local model. The model adapts the instructions to its own capabilities.

3. **Incremental, not redundant.** The manifest prevents duplicate work. Run it daily, weekly, or monthly — it only grabs what's new.

4. **Knowledge augmentation, not fine-tuning.** This doesn't retrain your model. It gives your model access to research it missed. The papers become context your model can reference.

5. **Zero cost by default.** arXiv is free. The instruction file is free. If you're running a local model, the entire pipeline costs nothing.

---

## Use Cases

| Who | Why |
|-----|-----|
| **AI practitioners** | Stay current without manually tracking papers |
| **Researchers** | Automated literature review starting from your model's knowledge gap |
| **Teams running AI employees** | Keep your workforce's knowledge base current |
| **Local model users** | Bridge the gap between small model training dates and today |
| **Anyone using AI for technical work** | Your model should know about DeepSeek-R1. Does it? |

---

## Production Use

This tool was built for production use, not as a demo. It emerged from an AI workforce system running 9 employees across sales, service, operations, and research roles.

**Our deployment:**
- 19 papers harvested on first run (August 2025 — February 2026)
- Papers stored locally, summaries integrated into employee knowledge bases
- Daily sync planned via automation
- Zero API cost

---

## Limitations & Things to Watch

- **Model capability matters.** This relies on the LLM's ability to search, download, and follow manifest logic. Frontier models (Claude Opus 4.6, o3, Gemini 2.5) do this reliably in practice. Smaller local models might hallucinate links or miss steps — but even partial success is better than nothing.
- **No built-in scheduling.** You have to manually prompt "run sync" or script it yourself. That said, wrapping it in a cron job or shell alias is trivial.
- **Summary quality scales with the model.** Stronger reasoners (Claude, o3) pick better papers and write better summaries than a 7B local model. Use what you have — upgrade the model later if you want better output.
- **arXiv is the primary source, not the only source.** Some breakthroughs land on HuggingFace blogs or Twitter before they hit arXiv. The instruction file supports adding sources, but out of the box it's arXiv-focused.

---

## Contributing

This started as an internal tool. If you're using it and find patterns that work — new sources, better filtering, improved summary prompts — contributions are welcome.

- Open an issue to discuss improvements
- Submit PRs for new sources or documentation
- Share your sync results (what did your model find useful?)

---

## License

MIT — use it, modify it, build on it.

---

*Built by [Jeff Vierra](https://github.com/jevierra) — Solutions Architect, AI Workforce Designer, 24 years in technology solutions.*
