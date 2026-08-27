# What's Missing — ATS Audit + Google DeepMind Gap Analysis

Audited against `Nafiz_Imtiaz_Khan_Resume.pdf` (3 pages) and `01-main.tex`, 2026-08-26.

---

## Part 1 — ATS Score

**Overall ATS parse score: 61 / 100.**
**Keyword match against a typical DeepMind Research Engineer / Research Scientist JD: ~45%.**

| Dimension | Score | Note |
|---|---|---|
| Machine parseability | 9 / 20 | Name and several keywords are destroyed on text extraction |
| Section structure & headers | 13 / 20 | Non-standard headers, missing an entire required section |
| Keyword coverage | 11 / 25 | Missing most of the vocabulary frontier-lab JDs screen on |
| Bullet quality & metrics | 17 / 20 | Strongest area. Numbers are real and specific |
| Consistency & hygiene | 11 / 15 | Date formats, typos, stray brackets |

---

## Part 2 — Hard ATS Defects (things that literally break the parse)

### 2.1 Your name does not parse — CRITICAL
`01-main.tex:239` uses `\scshape`. Text extraction yields:

```
N AFIZ I MTIAZ K HAN
```

Every ATS tokenizes that as six garbage tokens. Name-field extraction fails; some systems then reject the file or file you under a mangled name. This is the single highest-value fix in the document and it is one word of LaTeX.

**Fix:** delete `\scshape`, keep `\Huge\textbf`.

### 2.2 Line-break hyphenation is silently merging and splitting keywords — CRITICAL
LaTeX `\justifying` plus no hyphenation control means words break across lines and the extractor either splits or fuses them. Confirmed in the extracted text:

- `open-source` → extracted as **`opensource`** (keyword lost)
- `governance` → extracted as **`gov- ernance`**
- `documentation` → **`docu- mentation`**
- `integrating` → **`inte- grating`**

Every one of those is a term a recruiter or ATS would search for. You are losing keyword matches you already earned.

**Fix:** add `\usepackage[none]{hyphenat}\sloppy` to the preamble, or drop `\justifying` for ragged-right.

### 2.3 No undergraduate degree — CRITICAL
The Education section contains only UC Davis. Your BSc from MIST appears **nowhere** except obliquely in an Awards bullet ("academic excellence at MIST"). Consequences:

- Many ATS pipelines run a hard filter on "Bachelor's degree in CS or related field". You fail an automated requirement you actually meet.
- Google specifically asks for degree + institution + graduation year in its application form and cross-checks against the resume. A missing BSc reads as a gap or an inconsistency.
- It creates an unexplained 2021–2023 timeline hole.

**Fix:** add the MIST entry with degree name, field, dates, and CGPA.

### 2.4 Phone number format
`(+1)530-220-8037` — the glued `(+1)` prefix is not a format most parsers normalize. Use `+1 530-220-8037`.

---

## Part 3 — Structural / Content ATS Losses

### 3.1 "Career Objective" is the wrong header
Objective sections are deprecated and several ATS taxonomies do not map "Career Objective" to a known section type, so the content gets dropped from the summary field. Rename to **`Summary`** or **`Professional Summary`**. Also rewrite out of first person ("I am a PhD candidate…" → "PhD candidate in Computer Science at UC Davis…").

### 3.2 The BetterHelp bullet is empty content
> "Contributing to AI/ML initiatives as part of the Artificial Intelligence Intern team at BetterHelp."

This is your most recent and most prominent role and it contains zero keywords, zero technology, zero metrics. It reads as a placeholder. Right now it actively hurts you: a recruiter's eye lands there first and finds nothing. Replace with two bullets naming the models, stack, dataset scale, and one measured outcome.

### 3.3 Inconsistent date formats
- `September 2023 - Present`
- `Aug 2025 – Sep 2025`
- `July 2022 – August 2023`
- `June 2026 - Present`

Abbreviated vs. full months, hyphen vs. en dash. Date-range extractors mis-order roles when formats differ. Pick one: `Month YYYY – Month YYYY` throughout.

### 3.4 Inline raw URLs inside bullets
Bullets carry full `https://github.com/...` strings mid-sentence. This dilutes keyword density, forces bad line breaks (which is what triggers 2.2), and adds nothing for ATS since links are not indexed. Move project links to a compact Projects section or hyperlink the project name.

### 3.5 Dead placeholder tokens
`[LINK]` appears 20+ times and `[REF]` 3 times. These are literal text to an ATS. Hyperlink the title instead.

### 3.6 Typos and formatting errors
- "Champion in the **the** Medical Robotics Challenge" (`01-main.tex`, Awards)
- "**Award** & Honors" → "Awards and Honors" (ampersand is fine but the singular is a typo)
- `PCL-Fetcher ([https://…` — stray opening bracket
- `MammoGen-RAG ([https://…])` — stray brackets
- `F1Score`, `F1score`, `F1 gain`, `BERTScore` — four different renderings of score metrics. Standardize on `F1 score`.

### 3.7 Three pages with 16 publications
Fine for an academic application. For an industry ATS + 6-second recruiter scan, pages 2–3 are almost entirely publications. Consider a 2-page industry variant that cuts publications 9–16 (the pre-PhD Bangladesh-era ML papers, which are on unrelated topics: concrete beams, bolted connections, COVID tweets) down to a single line: "16 peer-reviewed publications, full list on Google Scholar."

---

## Part 4 — Missing Keywords (this is where the 45% match comes from)

Your Technical Skills block is a 2022-era web-and-data-science stack. Frontier-lab JDs screen on a different vocabulary. Every term below is missing from the document entirely:

**Frameworks and compute — the biggest gap**
- **JAX** — DeepMind's primary framework. Its absence is the single loudest signal on your resume that you have not worked in their environment.
- Flax, Haiku, Optax
- XLA, TPU, CUDA, Triton
- Distributed training, data parallelism, model parallelism, FSDP, DeepSpeed, ZeRO
- Ray, SLURM
- Mixed precision, gradient checkpointing

**Modeling vocabulary**
- Transformer architecture (you built them; the word "Transformer" appears once, in the last bullet)
- Attention, self-attention, tokenization
- Pretraining, post-training, instruction tuning
- RLHF, DPO, RLAIF, reward modeling
- Hugging Face, Transformers library, PEFT (PEFT appears once, buried), LoRA / QLoRA (buried)
- Multimodal, vision-language model (VLM appears once)
- Diffusion, reinforcement learning, agents, evaluation harness

**Engineering**
- Plain `SQL` as a standalone token (you list `PL/SQL` only, and many keyword matchers do not substring-match)
- CI/CD, unit testing, code review, MLOps
- Weights & Biases, MLflow, TensorBoard
- Go, Rust (nice-to-haves on Google reqs)

**Research signals**
- Benchmark, ablation, reproducibility, open-source contribution
- Peer review / reviewer for [venue] — you have none listed and this is a standard research-scientist line

---

## Part 5 — Why DeepMind Is Not Calling You

Blunt read. Your resume is strong, your publication record is genuinely strong, and none of that is the problem. The problem is **category mismatch**: you look like an *applied ML / software engineering researcher who uses LLM APIs*, and DeepMind hires people who *build the models*. Ranked by how much each one is costing you:

### 5.1 Zero JAX and zero large-scale training — the decisive gap
DeepMind runs on JAX. Beyond the keyword, nothing in your resume shows you have ever trained anything large. Every project is a pipeline built *on top of* someone else's model: LangChain orchestration, ChromaDB retrieval, Ollama inference, AWS Bedrock calls, PEFT/QLoRA fine-tuning of a small open model. That is legitimate, valuable applied work. It is not what a Research Engineer req is scoped to.

**What they want to see:** multi-node training, TPU/GPU cluster hours, parameter counts, throughput numbers, a scaling curve, a custom kernel, a training loop you wrote from scratch.
**What you show:** F1 deltas on task-specific pipelines.

### 5.2 Your research area is not one of theirs
Your first-author identity is **OSS sustainability forecasting** (FSE, ASE) and **clinical documentation automation** (AMIA, JAMIA Open). Both are real research areas with real venues. Neither is a DeepMind area. DeepMind publishes at NeurIPS, ICML, ICLR, and their applied science at Nature. You have zero publications at any core ML venue.

A DeepMind hiring manager scanning your Selected Publications sees software engineering conferences and medical informatics journals. They will conclude, correctly by their criteria, that you are an SE/health-informatics researcher, and route you to a different team or to reject. This is the deepest structural issue and the slowest to fix.

### 5.3 Your named baselines are dated and one looks wrong
- "10% F1 gain over the prior SOTA, **Claude 4.6**" — calling a commercial chat model "the prior SOTA" for angiography report generation is not how a research reviewer reads SOTA, and per your own AngioVision results the commercial baseline you actually beat was Opus 5. Verify this claim before it gets asked about in an interview, because it will be.
- "**GPT-4o**, **Claude 4.5 Sonnet**" as your RepoWise comparison set. In 2026 those are two-generation-old models. It dates the work and suggests the evaluation was not refreshed.

### 5.4 No brand-name engineering internship
Your industry history: a Bangladesh research center (2022–23), Scale AI, BetterHelp. The Scale AI role, "Human Frontier Collective Specialist – GenAI," is Scale's expert-contributor program, and a recruiter who knows Scale will read it as data annotation / red-teaming contract work, not engineering. It is on your resume in the #2 slot where it draws maximum attention to that read.

DeepMind's Research Engineer pipeline heavily favors candidates with a prior internship at DeepMind, Google Brain, FAIR, OpenAI, Anthropic, or an equivalent. You have none, and no referral signal on the page.

### 5.5 No open-source contributions to projects other people use
You have shipped a lot of repos: AngioVision, RepoWise, OSSPREY, ReACT-GPT, EvidenceBot, PCL-Fetcher, MammoGen-RAG. All are your own first-party research artifacts. There is no evidence of contributing to a widely-used library. For a lab that values engineering craft, a merged PR into JAX, Flax, Transformers, or vLLM is worth more than another solo repo, and it is the cheapest credential on this list to acquire.

### 5.6 Application-channel problem, not a resume problem
Worth naming honestly: DeepMind's cold-apply funnel is close to a lottery. Research Scientist roles are effectively closed to anyone without core-ML publications, and Research Engineer roles are dominated by referrals and returning interns. If you have been applying through the careers portal only, the resume may never have reached a human. No amount of ATS tuning fixes a channel problem.

---

## Part 6 — What To Do, In Priority Order

**Fix this week (mechanical, high return, no new work required)**
1. Remove `\scshape` so your name parses.
2. Kill hyphenation so `open-source` stops becoming `opensource`.
3. Add the MIST BSc entry.
4. Rewrite the BetterHelp bullets with real content.
5. Rename "Career Objective" → "Summary", drop first person.
6. Normalize dates, fix the typos and stray brackets, standardize `F1 score`.
7. Add the missing keywords you can honestly claim today: Transformer, attention, Hugging Face, Transformers, PEFT, LoRA, QLoRA, SQL, fine-tuning, multimodal, VLM, evaluation, benchmarking, distributed training if true, W&B if true.
8. Verify or correct the "Claude 4.6 SOTA" claim; refresh GPT-4o / Claude 4.5 Sonnet to current baselines.

Those eight take an afternoon and move the ATS score from **61 to roughly 88**.

**Fix this quarter (changes what you are, not how you are described)**
9. Learn JAX properly and ship one real artifact in it. Reimplement a paper, train something on TPU via Google's TRC free-TPU program, and put the parameter count, the hardware, and the throughput on the resume.
10. Land one merged PR in JAX, Flax, Hugging Face Transformers, or vLLM.
11. Submit one paper to a core ML venue. Your multimodal medical VLM work is the most transferable thing you have; a NeurIPS or ICML workshop paper is a realistic near-term target and changes the venue signal on your publication list.
12. Rewrite at least two bullets around scale rather than task accuracy: model size, tokens, hardware, wall-clock, throughput.

**Change the channel**
13. Stop relying on the careers portal. Target the specific team whose work overlaps yours (health / multimodal / AI-for-science), read their last two papers, and email the authors with something specific about their work. Use your advisor's network; Filkov's reach into Google is a real asset and costs you one email to activate.
14. Apply in parallel to Google Research and Google Health, where your health-informatics record is an asset instead of a mismatch, rather than the liability it is at DeepMind.

**A calibration note.** Your profile is a genuinely good fit for Google Research, Google Health, Microsoft Research Health Futures, and most applied-AI-in-medicine industry teams. It is a weak fit for DeepMind's core research orgs as your record currently stands, and the resume is not what is stopping you there. Items 9 through 12 are what would.
