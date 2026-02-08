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

**Self-assessment:** Before proceeding, honestly assess your capabilities:
- Can you search the web? (If no, you'll need the user to provide search results)
- Can you download files to the local filesystem? (If no, you'll output URLs for manual download)
- Can you read and write local files? (If no, you'll output all content for the user to save)

Adapt the remaining steps based on your actual capabilities. Partial execution is better than none.

**Record:**
- Model name: [your model]
- Training cutoff: [date]
- Today's date: [today]
- Knowledge gap: [cutoff] to [today]
- Capabilities: [search: yes/no] [download: yes/no] [file access: yes/no]

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

Search for significant AI research papers published in your knowledge gap (or since last sync).

**Primary source — arXiv:**

Search these categories:
- `cs.LG` — Machine Learning
- `cs.AI` — Artificial Intelligence
- `cs.CL` — Computation and Language
- `cs.CV` — Computer Vision
- `stat.ML` — Machine Learning (Statistics)

Use the arXiv API (`http://export.arxiv.org/api/query`) or web search to find papers. When using web search, query patterns like:
- `site:arxiv.org cs.LG [topic] 2025`
- `"arXiv" [topic] [year]`

**Secondary sources — check these too:**

Not everything lands on arXiv first. Some breakthroughs appear on blogs, model cards, or community platforms before (or instead of) a formal paper.

| Source | URL | What to look for |
|--------|-----|-----------------|
| HuggingFace Daily Papers | https://huggingface.co/papers | Curated, high-signal, community-ranked |
| Papers With Code | https://paperswithcode.com | Ties papers to implementations, trending section |
| Semantic Scholar | https://www.semanticscholar.org | Citation counts for ranking impact |
| OpenReview | https://openreview.net | Conference papers (NeurIPS, ICML, ICLR) |
| Major lab blogs | See list below | Model releases, technical reports |

**Lab blogs to check for non-arXiv releases:**
- Anthropic: https://www.anthropic.com/research
- OpenAI: https://openai.com/research
- Google DeepMind: https://deepmind.google/research/
- Meta AI: https://ai.meta.com/research/
- DeepSeek: https://github.com/deepseek-ai

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

---

## Step 4: Validate and Download Papers

**Before downloading, validate every paper:**

1. Confirm the arXiv ID is real — the format is `YYMM.NNNNN` (e.g., `2501.12948`)
2. Verify the URL resolves: `https://arxiv.org/abs/[arXiv_ID]`
3. Do NOT fabricate or guess arXiv IDs. If you're unsure an ID is correct, flag it for the user to verify instead of downloading blindly.

**For each validated paper:**

1. **Download the PDF** from arXiv: `https://arxiv.org/pdf/[arXiv_ID]`
2. **Save to** the `papers/` directory
3. **Filename format:** `arXiv_YYMM.NNNNN_title-short.pdf`
   - Use lowercase, hyphens for spaces
   - Keep title portion to 3-5 descriptive words
   - Example: `arXiv_2501.12948_deepseek-r1.pdf`

**For papers not on arXiv** (blog posts, direct PDF releases):
- Use format: `source_title-short.pdf`
- Example: `anthropic_claude-opus-4-system-card.pdf`

**After each download, verify the file:**
- Check file size is greater than 0 bytes
- Confirm it's a valid PDF (not an HTML error page)
- If a download fails or returns an error page, note it in the report and move on

**If you cannot download PDFs directly** (some models lack file system access):
- Output the full list of validated URLs so the user can download manually
- Include the suggested filename for each
- This is a valid outcome — don't skip the step, just adapt it

---

## Step 5: Update the Manifest

After downloading, update `manifest.md` with every paper downloaded in this session.

**If manifest.md doesn't exist, create it from this template:**

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

**If manifest.md already exists, update it:**
- Append new entries to the existing table (don't overwrite previous entries)
- Update the "Last sync" date and "Total papers" count
- Keep entries in chronological order by publication date
- Do NOT remove or modify existing entries

**Manifest is the source of truth.** If a paper is in the manifest, it doesn't get downloaded again. If the manifest is lost or corrupted, delete it and run a full sync to rebuild.

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

**Summary quality note:** If you're a smaller model and aren't confident in your summary accuracy, mark the summary with `**[Needs Review]**` so a human or stronger model can verify it later. An honest flag is better than a confident hallucination.

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
Papers failed (download errors): [count]
Papers flagged (unverified IDs): [count]
Manifest updated: manifest.md
Summaries: [generated / skipped]
Next run will cover: [today] onward.
```

---

## Automation (Optional)

This instruction file is designed to be run manually, but you can automate it.

**Cron job (daily sync at 6am):**
```bash
0 6 * * * cd /path/to/ai-research-sync && claude -p "Read sync.md and execute. Download papers and generate summaries."
```

**Shell alias (on-demand):**
```bash
alias ai-sync='cd /path/to/ai-research-sync && claude -p "Read sync.md and execute."'
```

**Ollama with a wrapper script:**
```bash
#!/bin/bash
cd /path/to/ai-research-sync
# Use arXiv API to fetch recent papers, then pass to local model for summarization
python3 fetch-arxiv.py --since "$(grep 'Last sync' manifest.md | cut -d' ' -f3-)" | \
  ollama run llama3 "Summarize each of these papers in 150-250 words..."
```

Adapt to your setup. The manifest logic stays the same regardless of how you trigger it.

---

## Configuration

**To customize which topics are prioritized**, edit the "Priority topics" list in Step 3.

**To add sources beyond the defaults**, add them to the source tables in Step 3.

**To change the download location**, modify the path in Step 4.

**To adjust paper count**, change the target numbers in Step 3.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Model doesn't know its cutoff | Ask the user or check the table in Step 1 |
| Model hallucinates arXiv IDs | Validate every ID before downloading (Step 4). If unsure, flag for user. |
| Can't download PDFs (no file access) | Output URLs for manual download — this is expected for some models |
| Downloaded file is 0 bytes or HTML | Delete it, note the failure, try an alternate URL or flag for user |
| arXiv rate limiting | Pause 3-5 seconds between downloads, or download in batches |
| Paper not on arXiv | Check the lab's blog or publisher site directly (see secondary sources in Step 3) |
| Manifest corrupted | Delete manifest.md and run a full sync to rebuild |
| Too many papers found | Increase selectivity — focus on major labs and high-citation papers |
| Smaller model missing papers | Run a frontier model periodically for a thorough sweep, use local model for daily checks |

---

*AI Research Sync — Keeping models current, one paper at a time.*
