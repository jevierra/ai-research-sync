# AI Research Sync — Instruction File

**Purpose:** You are an AI research harvester. Your job is to find, download, and catalog significant AI research papers published after your training cutoff date.

**Mode:** Execute these instructions step by step. Do not skip steps.

---

## Step 1: Identify Your Knowledge Gap

Determine your training data cutoff date. This is the date after which you have no training knowledge of published research.

**Known cutoffs (update as needed):**

| Model | Training Cutoff |
|-------|----------------|
| Claude Opus 4.6 | May 2025 |
| Claude Sonnet 4.5 | April 2025 |
| GPT-4o | October 2023 |
| GPT-4 Turbo | December 2023 |
| GPT-5 | Varies by version |
| Gemini 2.5 Pro | Varies |
| Llama 3.1 | December 2023 |
| Llama 4 | Varies |
| Mistral Large | Varies |
| Qwen 2.5 | Varies |

If your model is not listed, state your best estimate of your training cutoff. If unknown, ask the user or default to 6 months before today's date.

**Record:**
- Model name: [your model]
- Training cutoff: [date]
- Today's date: [today]
- Knowledge gap: [cutoff] to [today]

---

## Step 2: Check the Manifest

Read `manifest.md` in this directory. If it doesn't exist, this is a first run — skip to Step 3.

If the manifest exists:
- Note the **last sync date**
- Note all **arXiv IDs already downloaded**
- Your search range is: **last sync date** to **today** (not the full cutoff gap)
- You will skip any paper already in the manifest

---

## Step 3: Search for Papers

Search arXiv for significant AI research papers published in your knowledge gap (or since last sync).

**arXiv categories to search:**
- `cs.LG` — Machine Learning
- `cs.AI` — Artificial Intelligence
- `cs.CL` — Computation and Language
- `cs.CV` — Computer Vision
- `stat.ML` — Machine Learning (Statistics)

**Priority topics (rank higher):**
- New model releases and technical reports (DeepSeek, Llama, Qwen, Gemma, GPT, Gemini, Claude)
- Reasoning and chain-of-thought (o1-style, R1-style, MCTS, test-time compute)
- Agents and tool use
- Transformers and scaling laws
- Multimodal models (vision-language, audio-language)
- Diffusion models for language or generation
- Reinforcement learning (RLHF, RLAIF, GRPO, PPO for LLMs)
- Evaluation benchmarks and methodology
- Alignment, safety, and interpretability
- Attention mechanisms and efficiency (sparse attention, linear attention)
- Major breakthroughs in any AI subfield

**Selection criteria:**
- High download counts or citations
- Papers from major labs (DeepSeek, Meta, Google, Anthropic, OpenAI, Alibaba, Mistral)
- Clear novelty in title/abstract
- Papers that spawned significant follow-up work
- Conference best papers (NeurIPS, ICML, ICLR, ACL)

**Target:** 10-15 papers for a full backfill. 1-5 papers for an incremental sync.

**Additional sources to check:**
- HuggingFace Daily Papers (https://huggingface.co/papers)
- Papers With Code (https://paperswithcode.com)
- Semantic Scholar API for citation counts

---

## Step 4: Download Papers

For each selected paper:

1. **Download the PDF** from arXiv: `https://arxiv.org/pdf/[arXiv_ID]`
2. **Save to** the `papers/` directory
3. **Filename format:** `arXiv_YYMM.NNNNN_title-short.pdf`
   - Use lowercase, hyphens for spaces
   - Keep title portion to 3-5 descriptive words
   - Example: `arXiv_2501.12948_deepseek-r1.pdf`

For papers not on arXiv (blog posts, direct PDF releases):
- Use format: `source_title-short.pdf`
- Example: `anthropic_claude-opus-4-system-card.pdf`

**If you cannot download PDFs directly** (some models lack file system access):
- Output the list of URLs so the user can download manually
- Or use available tools (curl, wget) if you have shell access

---

## Step 5: Update the Manifest

After downloading, update `manifest.md` with every paper downloaded in this session.

**Manifest format:**

```markdown
# AI Research Sync — Manifest

**Last sync:** [today's date]
**Model used:** [your model name]
**Training cutoff:** [your cutoff date]
**Total papers:** [count]

---

## Downloaded Papers

| arXiv ID | Title | Published | Downloaded | Source | Filename |
|----------|-------|-----------|------------|--------|----------|
| [ID] | [Title] | [pub date] | [today] | [arXiv/other] | [filename.pdf] |
```

**Rules:**
- Append new entries to the existing table (don't overwrite previous entries)
- Update the "Last sync" date and "Total papers" count
- Keep entries in chronological order by publication date

---

## Step 6: Generate Summaries (Optional)

If instructed to summarize, create or append to `summaries.md`:

**For each paper, write:**

```markdown
### [Paper Title]
**Authors:** [First author et al.]
**arXiv:** [ID] — [link]
**Published:** [date]
**Priority:** [Normal / URGENT]

**Summary:** [150-250 words: what the paper does, methodology, key results]

**Key Insights:**
- [Most important contribution]
- [Second key finding]

**Why It Matters:** [1-2 sentences on significance for current AI development]

---
```

**Mark as URGENT if:**
- New frontier model release
- Major scaling law discovery
- Breakthrough in reasoning or agentic capabilities
- Significant safety/alignment finding
- Paradigm-shifting technique

---

## Step 7: Report

When complete, output:

```
Sync complete.
Model: [name]
Training cutoff: [date]
Sync range: [start date] to [end date]
Papers downloaded: [count]
Papers skipped (already in manifest): [count]
Manifest updated: manifest.md
Summaries: [generated / skipped]
Next run will cover: [today] onward.
```

---

## Configuration

**To customize which topics are prioritized**, edit the "Priority topics" list in Step 3.

**To add sources beyond arXiv**, add them to the "Additional sources" section in Step 3.

**To change the download location**, modify the path in Step 4.

**To adjust paper count**, change the target numbers in Step 3.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Model doesn't know its cutoff | Ask the user or check the table in Step 1 |
| Can't download PDFs (no file access) | Output URLs for manual download |
| arXiv rate limiting | Pause between downloads, or download in batches |
| Paper not on arXiv | Check the publisher's site directly |
| Manifest corrupted | Delete manifest.md and run a full sync |
| Too many papers found | Increase selectivity — focus on major labs and high-citation papers |

---

*AI Research Sync — Keeping models current, one paper at a time.*
