# 03. Foundation Model Benchmarks

> **Purpose** — Understand the major public benchmarks used to evaluate foundation models, how to read their scores, and why they must be complemented by application-level evals before shipping.

---

## Table of Contents

- [Why Benchmarks Matter](#why-benchmarks-matter)
- [Benchmark Categories](#benchmark-categories)
- [Key Benchmarks at a Glance](#key-benchmarks-at-a-glance)
  - [Classic Foundation Model Benchmarks](#classic-foundation-model-benchmarks)
  - [Modern Frontier & Agentic Benchmarks (2025–2026)](#modern-frontier--agentic-benchmarks-20252026)
- [Benchmark Deep Dives — Classic Foundation Tests](#benchmark-deep-dives--classic-foundation-tests)
- [Benchmark Deep Dives — Modern Agentic & Enterprise Benchmarks](#benchmark-deep-dives--modern-agentic--enterprise-benchmarks)
  - [1. AA Intelligence Index](#1-aa-intelligence-index)
  - [2. GDPVal-AA v2](#2-gdpval-aa-v2)
  - [3. CursorBench v3.2](#3-cursorbench-v32)
  - [4. DeepSWE v1.1](#4-deepswe-v11)
  - [5. FrontierCode v1.1 (Extended)](#5-frontiercode-v11-extended)
  - [6. APEX-Agents](#6-apex-agents)
  - [7. Terminal-Bench v3.0](#7-terminal-bench-v30)
  - [8. APEX-SWE](#8-apex-swe)
  - [9. AA-Briefcase](#9-aa-briefcase)
  - [10. Harvey LAB (Vals)](#10-harvey-lab-vals)
- [Interpreting Benchmark Scores](#interpreting-benchmark-scores)
- [Common Pitfalls](#common-pitfalls)
- [Benchmarks vs Application Evals](#benchmarks-vs-application-evals)
- [Leaderboard Resources](#leaderboard-resources)
- [Further Reading](#further-reading)
- [Guidance](#guidance)

---

## Why Benchmarks Matter

Benchmarks provide a **standardized, reproducible signal** for comparing models across capabilities. They serve three primary purposes:

1. **Model selection** — Narrow the field before running your own evals.
2. **Regression detection** — Track whether a model update improves or degrades core capabilities.
3. **Community alignment** — Give researchers a shared vocabulary to discuss progress.

> ⚠️ **Benchmarks are proxies, not ground truth.** A model that tops a leaderboard can still fail catastrophically on your specific task, distribution, or risk profile.

---

## Benchmark Categories

| Category | What It Tests | Example Benchmarks |
|---|---|---|
| **Reasoning** | Logical deduction, multi-step problem solving | GPQA, ARC, BBH |
| **Knowledge** | Factual recall across domains | MMLU, MMLU-Pro |
| **Math** | Numerical computation, proof-style reasoning | GSM8K, MATH, MathVista |
| **Coding (Synthetic / Unit)** | Function completion, algorithmic bug fixing | HumanEval, MBPP |
| **Agentic Coding (IDE & PR)** | Full-repo editing, PR mergeability, IDE interaction | CursorBench v3.2, DeepSWE v1.1, FrontierCode v1.1, SWE-Bench |
| **Terminal & Systems / DevOps** | Bash execution, Linux admin, incident debugging | Terminal-Bench v3.0, APEX-SWE |
| **Enterprise Knowledge Work** | Multi-file business deliverables, financial/strategic analysis | GDPVal-AA v2, AA-Briefcase, APEX-Agents |
| **Domain-Specific (Legal)** | Complex legal drafting, statutory analysis, compliance | Harvey LAB (Vals) |
| **Composite Frontier Indices** | Weighted multi-pillar evaluations resisting saturation | AA Intelligence Index |
| **Truthfulness** | Avoiding common misconceptions & hallucinations | TruthfulQA |
| **Language Understanding** | Sentence completion, NLI, commonsense | HellaSwag, WinoGrande, ARC |
| **Long Context** | Retrieval & reasoning over large input windows | RULER, Needle-in-a-Haystack, LongBench |
| **Multimodal** | Vision-language understanding, visual QA | MMMU, MathVista, VQAv2 |
| **Instruction Following** | Adherence to complex, multi-constraint instructions | IFEval, MT-Bench |
| **Safety & Alignment** | Refusal of harmful prompts, bias measurement | ToxiGen, BBQ, HarmBench |
| **Agentic / Tool Use** | Multi-step tool calling, environment interaction | SWE-Bench, WebArena, ToolBench, APEX-Agents |

---

## Key Benchmarks at a Glance

### Classic Foundation Model Benchmarks

| Benchmark | Category | Format | Metric | Approx. Scale | Notes |
|---|---|---|---|---|---|
| **MMLU** | Knowledge | 57-subject MCQ | Accuracy (%) | 14 k questions | De facto standard; watch for data contamination |
| **MMLU-Pro** | Knowledge | Harder MCQ (10 options) | Accuracy (%) | 12 k questions | Reduced guessing via more answer choices |
| **GPQA** | Reasoning | Graduate-level science MCQ | Accuracy (%) | 448 questions | "Diamond" subset is expert-validated |
| **ARC** (Challenge) | Reasoning | Grade-school science MCQ | Accuracy (%) | 2.6 k questions | Tests commonsense + science reasoning |
| **HellaSwag** | Language | Sentence completion MCQ | Accuracy (%) | 10 k questions | Near-saturated; useful as a baseline |
| **TruthfulQA** | Truthfulness | Open-ended + MCQ | % truthful | 817 questions | Tests resistance to popular misconceptions |
| **HumanEval** | Coding | Function completion (Python) | pass@k | 164 problems | Simple functions; use HumanEval+ for stricter tests |
| **MBPP** | Coding | Short Python programs | pass@k | 974 problems | Broader than HumanEval |
| **SWE-Bench** | Coding (Agentic) | Real GitHub issues → patches | % resolved | 2.3 k instances | Gold standard for agentic coding; use "Verified" subset |
| **GSM8K** | Math | Grade-school word problems | Accuracy (%) | 8.5 k problems | Near-saturated for frontier models |
| **MATH** | Math | Competition-level problems | Accuracy (%) | 12.5 k problems | Levels 1-5 difficulty |
| **LiveBench** | Mixed | Regularly refreshed questions | Composite score | Varies monthly | Designed to resist contamination |
| **MT-Bench** | Instruction | Multi-turn conversation | LLM-judge score (1-10) | 80 questions | Uses GPT-4 as judge |
| **IFEval** | Instruction | Verifiable constraint following | Accuracy (%) | 541 prompts | Objective, rule-based scoring |
| **MMMU** | Multimodal | College-level vision MCQ | Accuracy (%) | 11.5 k questions | Spans 30 subjects |
| **Needle-in-a-Haystack** | Long Context | Retrieval from long docs | Recall (%) | Configurable length | Tests effective context window |

### Modern Frontier & Agentic Benchmarks (2025–2026)

As foundation models matured and legacy academic benchmarks saturated, the AI community shifted toward **agentic, multi-hour, production-grade benchmarks**. These benchmarks evaluate autonomous action within real operating environments (IDEs, bash terminals, enterprise filesystems, and telemetry platforms) and emphasize **strict anti-contamination safeguards**:

| Benchmark | Creator / Host | Focus Domain | Environment & Interaction | Key Metric | Anti-Contamination Strategy |
|---|---|---|---|---|---|
| **AA Intelligence Index** | Artificial Analysis | Composite frontier intelligence | Multi-benchmark aggregation (Agents, Coding, Math, Knowledge) | Index Score (0–100) | Dynamic framework updates; swaps out saturated tests; independent runs |
| **GDPVal-AA v2** | Artificial Analysis (OpenAI base) | Economically valuable knowledge work across 44 professions | Sandboxed agentic shell, browser, and multi-file workspace | Deliverable Elo & Rubric Grade | Held-out task suites; blind expert pairwise comparisons; no web leaks |
| **CursorBench v3.2** | Cursor / Anysphere | Real-world IDE-native agentic coding & developer workflows | Cursor Composer / Agent IDE architecture | Solution Correctness (pass@k), Code Quality, Latency | Derived from real telemetry with "Cursor Blame"; anti-leakage filters |
| **DeepSWE v1.1** | Datacurve / Artificial Analysis | Long-horizon software engineering in active repos | Isolated multi-language Docker containers | % Resolved (Pass all test suites) | 100% authored from scratch; stripped git history; hidden verification suites |
| **FrontierCode v1.1 (Extended)** | Cognition AI | Production-grade code quality & PR mergeability | Complex multi-repository open-source codebases | Mergeability Rubric (Correctness, Scope, Style) | Private codebases; expert maintainer rubrics; extended multi-repo tasks |
| **APEX-Agents** | Mercor | High-value professional white-collar services | Virtual desktop with files, models, slides, PDFs, emails | Expert Human Rubric (0–100) & Milestones | Private enterprise dossiers; multi-hour cross-application workflows |
| **Terminal-Bench v3.0** | T-Bench / Vals / OpenAI / AA | Terminal CLI execution, DevOps, & systems engineering | Sandboxed Linux/Unix bash shell | Container State Assertions (% Passed) | Programmatic environment checks; ephemeral Docker isolation; dynamic tasks |
| **APEX-SWE** | Mercor & Cognition AI | Cloud system integrations & live telemetry debugging | Distributed services & observability stack (Grafana, Loki) | Incident MTTR, Root-Cause Fix %, SLA Restoration | Private production environments; live metric/log streams |
| **AA-Briefcase** | Artificial Analysis | Long-horizon enterprise projects with massive context | Enterprise data room (1,000s of corporate documents) | Multi-Criterion Rubric & Output Fidelity | Held-out proprietary enterprise dossiers; formula verification |
| **Harvey LAB (Vals)** | Harvey AI & Vals.ai | High-stakes legal practice & statutory analysis | Multi-practice legal document drafting & due diligence | Partner-Grade Rubric & Doctrinal Accuracy | Evaluated blindly in-house by Vals.ai; 100% private held-out data |

---

## Benchmark Deep Dives — Classic Foundation Tests

### Reasoning — GPQA

- **Full name**: Graduate-Level Google-Proof QA
- **What it tests**: Expert-level reasoning in biology, chemistry, and physics
- **Why it matters**: Questions are hard enough that domain experts without the specific sub-specialty score ~65%, making it a strong ceiling test
- **Key variant**: The **GPQA Diamond** subset (198 questions) is double-validated by experts and is the most cited split

### Knowledge — MMLU / MMLU-Pro

- **MMLU** (Massive Multitask Language Understanding) covers 57 subjects from abstract algebra to world religions
- **Known issues**: Some answer keys contain errors; 4-option MCQ allows ~25% random-guess baseline
- **MMLU-Pro** mitigates this by using 10 options per question and integrating more reasoning-intensive questions, making it more discriminating for frontier models

### Coding — HumanEval & SWE-Bench

| Aspect | HumanEval | SWE-Bench (Verified) |
|---|---|---|
| Scope | Single function | Full repository patch |
| Language | Python only | Multi-language |
| Interaction | One-shot generation | Agentic (multi-step) |
| Realism | Low (toy problems) | High (real GitHub issues) |
| Metric | pass@1 / pass@10 | % Resolved |

- **HumanEval** is easy to run but does not reflect production coding tasks
- **SWE-Bench Verified** (500 instances, human-validated) is the most reliable subset for comparing agentic coding systems

### Math — GSM8K & MATH

- **GSM8K**: Grade-school word problems. Frontier models now score >95%, making it primarily a sanity check
- **MATH**: Competition-level problems across 7 subjects (algebra, geometry, number theory, etc.). Scored by difficulty level (1-5). Much more discriminating than GSM8K

### Truthfulness — TruthfulQA

- Tests whether a model reproduces common human misconceptions (e.g., "We only use 10% of our brains")
- Scored in two dimensions: **truthfulness** and **informativeness** — a model that always says "I don't know" would be truthful but not informative
- Important limitation: the 817-question set is static and likely contaminated in modern training data

### Long Context — Needle-in-a-Haystack & RULER

- **Needle-in-a-Haystack**: Inserts a target fact at various positions within a long document and tests recall. Simple but effective as a smoke test
- **RULER**: More comprehensive — tests multi-hop reasoning, variable tracking, and aggregation across long contexts, not just retrieval
- **Key insight**: A model may pass retrieval tests at 128K tokens but fail reasoning tasks at the same length

### Contamination-Resistant — LiveBench

- Refreshes questions monthly using recent data sources
- Avoids LLM judges entirely — uses objective, verifiable answers
- Categories: math, coding, reasoning, language, instruction following, data analysis
- **Why it matters**: Addresses the biggest weakness of static benchmarks — training data contamination

---

## Benchmark Deep Dives — Modern Agentic & Enterprise Benchmarks

The 2025–2026 evaluation landscape marked a structural shift away from multiple-choice static exams toward **autonomous agents executing economically valuable, multi-hour workflows** within real software, system, and enterprise environments. Below are deep dives into the 10 modern frontier benchmarks:

```
┌─────────────────────────────────────────────────────────────────────────────────────────────────┐
│                      MODERN AGENTIC BENCHMARK TAXONOMY (2025–2026)                              │
├───────────────────────────────┬─────────────────────────────────┬───────────────────────────────┤
│    Software Engineering       │   Enterprise & Knowledge Work   │     Composite & Domain        │
├───────────────────────────────┼─────────────────────────────────┼───────────────────────────────┤
│ • CursorBench v3.2 (IDE-flow) │ • GDPVal-AA v2 (Economic work)  │ • AA Intelligence Index (All) │
│ • DeepSWE v1.1 (Clean SWE)    │ • AA-Briefcase (Long-horizon)   │ • Harvey LAB / Vals (Legal)   │
│ • FrontierCode v1.1 (PR Merge)│ • APEX-Agents (White-collar)    │                               │
│ • Terminal-Bench v3.0 (Shell) │                                 │                               │
│ • APEX-SWE (Telemetry/DevOps) │                                 │                               │
└───────────────────────────────┴─────────────────────────────────┴───────────────────────────────┘
```

---

### 1. AA Intelligence Index

- **Developer / Host**: [Artificial Analysis](https://artificialanalysis.ai/) (Independent AI evaluation authority)
- **What it tests**: Holistic, multi-modal frontier model intelligence across four balanced pillars: **Agents & Tool Use** (~30–34%), **General Reasoning & Broad Knowledge** (~18–30%), **Production Coding** (~20–24%), and **Scientific / Mathematical Reasoning** (~20–24%).
- **Evaluation Mechanism**:
  - Rather than relying on self-reported vendor scores or single-metric leaderboards, Artificial Analysis independently executes a suite of approximately 10 frontier benchmarks under standardized temperatures and deterministic harnesses.
  - Sub-evaluations include: *GDPVal-AA*, *AA-Briefcase*, *Terminal-Bench*, *Humanity's Last Exam (HLE)*, *GPQA Diamond*, and *SciCode*.
  - Raw sub-scores are normalized using calibrated clamp functions against Elo ratings (e.g., `clamp((Elo - 500) / 2000)`) to yield a unified composite score from 0 to 100.
- **Why it matters**: Solves the "benchmark expiration" dilemma. Static leaderboards become obsolete as models saturate tests; the AA Intelligence Index dynamically deprecates saturated benchmarks and swaps in higher-ceiling evaluations across iterative releases (v4.0 → v4.1.1 → v4.3).

---

### 2. GDPVal-AA v2

- **Developer / Host**: Artificial Analysis (expanded from OpenAI's original GDPval initiative)
- **What it tests**: Autonomous execution of **economically valuable knowledge work** corresponding to human labor across 44 occupational categories and 9 global industries (Finance, Corporate Law, Management Consulting, Healthcare, Marketing, Software Architecture, Public Policy, etc.).
- **Environment & Interaction Model**:
  - Agents are initialized in a live sandbox equipped with bash shell execution, web browsing, Python interpreters, and file system read/write access.
  - Prompts are complex enterprise briefs (e.g., *"Build an integrated three-statement financial valuation model under dual tariff shock scenarios and generate an executive presentation with sensitivity tables"*).
  - Agents output authentic production deliverables: fully calculated Excel workbooks (`.xlsx`), formatted slide decks (`.pptx`), and formal briefing memos (`.docx`).
- **Scoring & Metrics**:
  - **Deliverable Elo Rating**: Derived from extensive blind, pairwise comparisons by domain-expert human reviewers and calibrated judge agents.
  - **Multi-Factor Rubric**: Assesses analytical depth, financial/statutory formula correctness, visual formatting fidelity, and factual consistency.
- **v2 Enhancements**: Upgraded in late 2025/2026 to introduce noisy input data rooms, ambiguous multi-step enterprise specifications, and strict anti-memorization safeguards.

---

### 3. CursorBench v3.2

- **Developer / Host**: Cursor / [Anysphere](https://www.cursor.com/)
- **What it tests**: Real-world **IDE-native agentic coding** — measuring how effectively AI agents navigate complex developer workspaces, write maintainable patches, and interact inside code editor environments (such as Cursor Composer / Agent CLI).
- **Data Provenance ("Cursor Blame")**:
  - Synthetic benchmarks (HumanEval) and public PR scrapes (SWE-bench) suffer from toy simplicity or training data memorization.
  - CursorBench derives tasks directly from anonymized, real-world developer edit sessions using a proprietary technique called **"Cursor Blame"**, which traces committed production patches backwards to original natural language requests and editor context.
- **Four Core Evaluation Pillars**:
  1. **Solution Correctness**: Multi-language test suite execution across 200+ languages and frameworks in sandboxed runtimes.
  2. **Code Quality**: Cyclomatic complexity analysis, duplicate logic detection, and architectural style consistency.
  3. **Efficiency & Speed**: First-token latency, total interaction rounds, and token-cost efficiency per solved issue.
  4. **Developer Interaction Fidelity**: Suggestion adoption rates and minimization of unnecessary code rewrites ("code churn").
- **v3.2 Enhancements**: Introduced advanced data contamination detection, private held-out enterprise repositories, and multimodal UI-to-code verification.

---

### 4. DeepSWE v1.1

- **Developer / Host**: [Datacurve](https://datacurve.ai/) (adopted by Artificial Analysis for the Coding Agent Index)
- **What it tests**: **Contamination-resistant, long-horizon software engineering** in active open-source ecosystems.
- **The Contamination Problem with SWE-Bench**:
  - Standard SWE-bench problems are mined from historic, public GitHub pull requests. Frontier models frequently memorize these exact PR diffs, discussions, and unit tests during web pretraining.
- **The DeepSWE Architecture**:
  - Consists of **113 original, multi-step engineering tasks** authored completely from scratch across 5 modern enterprise programming languages: TypeScript, Go, Python, JavaScript, and Rust.
  - Authored directly inside active, contemporary repositories by senior software engineers specifically for evaluation, ensuring zero overlap with public pretraining corpora.
- **v1.1 Safeguards & Anti-Cheating**:
  - **Hermetic Docker Runtimes**: Full network isolation during test evaluation.
  - **Stripped Git History**: Git commit logs and reflogs are stripped and sanitized, preventing agents from extracting golden patches from repository metadata.
  - **Adversarial & Hidden Test Suites**: Hidden regression suites prevent agents from gaming assertions by mocking test runners or hardcoding return constants.
- **Metric**: `% Resolved` — all existing repository tests and private withheld test suites must pass cleanly.

---

### 5. FrontierCode v1.1 (Extended)

- **Developer / Host**: [Cognition AI](https://www.cognition.ai/) (creators of Devin)
- **What it tests**: **Production-grade code quality and pull request mergeability** against the editorial standards of senior open-source maintainers and tech leads.
- **Why Passing Unit Tests Isn't Enough**:
  - Traditional benchmarks consider an issue solved if a unit test passes. However, models often pass tests by writing hacky workarounds, adding redundant dependencies, polluting global state, or introducing major architectural regressions.
- **Evaluation Rubric**:
  1. **Functional Correctness**: Comprehensive bug resolution verified by sandboxed test suites.
  2. **Blast Radius & Scope Control**: Strictly penalizes sprawling diffs, unnecessary file reformatting, dead code, or hallucinated files.
  3. **Idiomatic Style & Architectural Fit**: Strict compliance with repository linters, typing systems, and architectural design patterns.
  4. **Production Maintainability**: Human-maintainer review scoring whether the PR would be merged or rejected in a top-tier open-source project.
- **Extended Split**: Tests challenging long-horizon engineering challenges involving cross-repository refactors, major framework version migrations, and complex asynchronous state machines.

---

### 6. APEX-Agents

- **Developer / Host**: [Mercor](https://mercor.com/)
- **What it tests**: High-stakes **cross-application professional white-collar workflows** simulating the day-to-day labor of investment banking analysts, management consultants, and corporate attorneys.
- **Environment & Simulation**:
  - Agents are placed in a realistic simulated corporate desktop with complete filesystem access containing real-world artifacts: audited 10-K/10-Q SEC filings, messy enterprise Excel models, slide decks, client email threads, and scheduling calendars.
  - Tasks require multi-hour cognitive endurance, cross-file context tracking, and synthesis of disparate quantitative and qualitative signals.
- **Typical Task Profiles**:
  - Performing full Discounted Cash Flow (DCF) and Leveraged Buyout (LBO) valuation models with linked dynamic schedules.
  - Cross-referencing multi-hundred-page regulatory filings to draft antitrust risk summaries.
  - Synthesizing customer churn data from raw CSV exports into C-suite PowerPoint presentations.
- **Scoring & Metrics**:
  - **Expert Human Rubric (0–100)**: Evaluated by former Wall Street analysts, McKinsey/BCG consultants, and corporate lawyers.
  - **Programmatic Invariant Verification**: Validates formula integrity, balance sheet balancing, and citation accuracy.

---

### 7. Terminal-Bench v3.0

- **Developer / Host**: Joint community initiative ([T-Bench](https://tbench.ai/), Vals.ai, OpenAI contributors, and Artificial Analysis)
- **What it tests**: Autonomous **command-line interface (CLI) execution, Linux systems administration, DevOps orchestration, and security auditing**.
- **Task Domains**:
  - *DevOps & Infrastructure*: Orchestrating Docker Compose multi-container networks, diagnosing Kubernetes Pod CrashLoopBackOff states, configuring systemd daemons, and tuning Nginx reverse proxies.
  - *Systems Administration & OS*: Configuring Linux kernel parameters, resolving complex POSIX file permission/ACL bottlenecks, and managing storage volumes.
  - *Data & Systems Compilation*: Building complex C++/Rust projects from source with missing shared libraries, setting up replication across PostgreSQL clusters, and diagnosing broken iptables firewall routing.
- **v3.0 Advancements**:
  - Substantially elevates difficulty over v2.x with hardened multi-step failure injection.
  - Imposes tight wall-clock time and token budgets.
  - Demands dynamic error-stream parsing: when a bash command exits non-zero, the agent must parse stderr, inspect logs, formulate a debugging hypothesis, and recover autonomously.
- **Metric**: **Programmatic End-State Assertions (% Passed)** — pristine Docker containers verify filesystem changes, active network sockets, process tables, and command outputs.

---

### 8. APEX-SWE

- **Developer / Host**: Mercor in partnership with Cognition AI
- **What it tests**: **Real-world, economically valuable enterprise software engineering** beyond unit-level algorithmic problem solving, focusing on two dominant industry pain points:
  1. **Distributed System Integrations**: Building end-to-end integration bridges across microservices, cloud APIs, payment gateways (e.g., Stripe webhooks), message brokers (Kafka/RabbitMQ), and OAuth2/SAML auth systems.
  2. **Live Observability & Incident Remediation**: Debugging live production outages using industry-standard telemetry stacks.
- **Environment & Telemetry Harness**:
  - Agents are dropped into live, running distributed environments experiencing active, injected production failures (e.g., memory leaks, database connection pool exhaustion, cascading HTTP 504 timeouts, thread deadlocks).
  - Agents must query telemetry tools — inspecting **Grafana dashboards**, querying **Prometheus time-series metrics**, and parsing **Loki / Datadog log streams** — to identify root causes.
- **Metrics**:
  - **Mean Time to Detect & Resolve (MTTR)**.
  - **Root-Cause Fix Correctness (%)**.
  - **SLO / SLA Restoration Verification** without triggering regressions.

---

### 9. AA-Briefcase

- **Developer / Host**: Artificial Analysis
- **What it tests**: **Long-horizon enterprise project execution** requiring deep reasoning across massive corporate data rooms.
- **Evaluation Concept**:
  - While single-turn benchmarks evaluate short answers and GDPVal tests single deliverables, AA-Briefcase evaluates an agent acting as a virtual enterprise associate across **multi-week, multi-phase corporate initiatives**.
- **Input Corpus Scale**:
  - Agents are given access to virtual "briefcases" containing thousands of pages of raw enterprise documents: contracts, merger agreements, audited annual reports, customer call transcripts, compliance filings, and internal memos.
- **Workflows Evaluated**:
  - Comprehensive M&A due diligence investigations with automated redline generation.
  - Multi-tier supply chain risk modeling under geopolitical tariff disruptions.
  - Multi-jurisdiction corporate tax strategy synthesis.
- **Scoring**:
  - **Deterministic Formula Auditing**: Automated headless checkers verify arithmetic integrity across spreadsheets and financial schedules.
  - **Calibrated Expert Rubrics**: Evaluates strategic insight, legal hazard identification, and executive clarity.

---

### 10. Harvey LAB (Vals)

- **Developer / Host**: [Harvey AI](https://www.harvey.ai/) (premier legal AI platform) in partnership with [Vals.ai](https://vals.ai/)
- **What it tests**: **High-stakes domain-specific legal reasoning, statutory analysis, and contract engineering**.
- **The Zero-Contamination "Vals" Guarantee**:
  - Legal datasets on the public internet are heavily ingested into foundation model pre-training corpora.
  - Under the **Vals.ai** framework, the Harvey LAB suite is maintained as a **100% private, held-out evaluation**. Evaluations are executed blindly by Vals.ai in a secure environment; model weights or API endpoints are tested without model creators ever accessing the test cases or rubrics.
- **Scope & Practice Areas**:
  - Over 120 complex, authentic legal workflows spanning 24 practice areas: M&A, Capital Markets, Securities Compliance, Intellectual Property Litigation, Labor & Employment, Tax Law, and Antitrust.
- **Task Typologies**:
  - Drafting comprehensive Disclosure Schedules for private equity acquisition agreements from disorganized diligence folders.
  - Synthesizing multi-thousand-page deposition transcripts to identify contradictory witness statements for courtroom cross-examination.
  - Resolving conflicting appellate circuit splits to draft persuasive appellate briefs.
- **Scoring**:
  - Evaluated against partner-grade rubrics authored by former AmLaw 100 partners and federal judicial clerks.
  - Scores heavily penalize hallucinated legal precedents or inaccurate statutory citations while rewarding nuanced jurisdictional distinction and risk-hedged drafting.

---

## Interpreting Benchmark Scores

### Dos ✅

| Practice | Why |
|---|---|
| Compare models on the **same benchmark version and split** | Scores across versions are not comparable |
| Check the **evaluation harness** used (lm-eval, simple-evals, etc.) | Prompt format and sampling settings can shift scores by 5-10% |
| Look at **confidence intervals** or variance across runs | Single-run numbers can be noisy, especially on small benchmarks |
| Prefer **contamination-resistant** benchmarks for frontier comparisons | Static benchmarks get memorized over time |
| Weight benchmarks **closest to your use case** | A coding benchmark matters more if you're building a coding tool |

### Don'ts ❌

| Anti-Pattern | Why It's Dangerous |
|---|---|
| Ranking models by a **single benchmark** | Models have different strength profiles |
| Trusting **self-reported scores** without reproduction | Prompt engineering and cherry-picked configs inflate results |
| Ignoring **saturation** | When top models cluster at 95%+, the benchmark loses discriminative power |
| Conflating **benchmark performance with production readiness** | Benchmarks test capability, not reliability under your constraints |
| Using **exact-match accuracy on open-ended tasks** | Penalizes valid alternative phrasings |

---

## Common Pitfalls

### 1. Data Contamination
Models trained on web-scale corpora may have memorized benchmark questions. Scores on older benchmarks (MMLU, HellaSwag, GSM8K) are increasingly unreliable as true measures of capability.

**Mitigation**: Prefer benchmarks with anti-contamination mechanisms (LiveBench, private held-out splits) or design your own eval sets.

### 2. Prompt Sensitivity
A model's score can change dramatically based on:
- System prompt phrasing
- Few-shot example selection and ordering
- Answer extraction method (regex, constrained decoding, etc.)

**Mitigation**: Always document the exact prompt template and parsing strategy used.

### 3. Metric Gaming
- **pass@k with high k** inflates coding scores (generating 100 samples and checking if any pass is very different from getting it right on the first try)
- **Chain-of-thought prompting** can boost MCQ accuracy but may not transfer to real tasks

**Mitigation**: Report pass@1 as the primary metric. Be explicit about prompting strategy.

### 4. Benchmark Saturation
When the top 10 models all score within 1-2% of each other, the benchmark is no longer useful for discriminating between them. In 2025–2026, evaluation has decisively bifurcated between saturated legacy tests and the active frontier of agentic benchmarks:

| Benchmark | Saturation Status (2025–2026) | Role in Evaluation |
|---|---|---|
| **HellaSwag** | 🔴 Saturated (>95%) | Obsolete for frontier models; basic sanity check only |
| **GSM8K** | 🔴 Saturated (>98%) | Near-trivial for reasoning models; replaced by MATH / AIME |
| **HumanEval** | 🔴 Saturated (>90% pass@1) | Saturated for modern agent loops; replaced by repo-level benchmarks |
| **MMLU** | 🟡 Saturated / Contaminated | Approaching ceiling; high data contamination risk |
| **MMLU-Pro / GPQA Diamond** | 🟢 Discriminating | Useful for high-level scientific and academic reasoning |
| **SWE-Bench Verified** | 🟢 Discriminating | Established baseline for agentic repo-level coding |
| **CursorBench v3.2** | 🟢 High Discrimination | Active frontier for real-world developer IDE workflows |
| **DeepSWE v1.1** | 🟢 High Discrimination | Solves SWE-Bench contamination via zero-leakage authored tasks |
| **Terminal-Bench v3.0** | 🟢 High Discrimination | High ceiling for bash, DevOps, systems, and error-recovery |
| **APEX-SWE & APEX-Agents** | 🟢 High Discrimination | Active frontier for distributed debugging & multi-hour white-collar labor |
| **GDPVal-AA v2 & AA-Briefcase** | 🟢 High Discrimination | Active frontier for multi-step enterprise knowledge work |
| **Harvey LAB (Vals)** | 🟢 High Discrimination | Active frontier for partner-grade legal and regulatory reasoning |
| **AA Intelligence Index** | 🔄 Continuously Calibrated | Solves saturation by dynamically replacing ceiling-hit components |

---

## Benchmarks vs Application Evals

This is the central message of this module:

| Dimension | Benchmarks | Application Evals |
|---|---|---|
| **Purpose** | Compare general model capabilities | Validate fitness for your specific product |
| **Data** | Public, standardized (or third-party audited) | Private, task-specific |
| **Distribution** | Academic / synthetic / generalized tasks | Your actual user inputs & production telemetry |
| **Metrics** | Accuracy, pass@k, Elo, container assertions | Task success, user satisfaction, cost, latency |
| **Contamination Risk** | High for static public tests; medium for private sets | Zero (proprietary enterprise data) |
| **Who runs them** | Model providers, benchmark labs (AA, Vals) | Your engineering team |
| **When to use** | Model selection & shortlisting | Before every deployment & continuous monitoring |

> **Bottom line**: Use benchmarks to **shortlist models**. Use application evals to **make shipping decisions**. Never ship based on benchmark scores alone.

---

## Leaderboard Resources

| Resource | URL | Notes |
|---|---|---|
| **Artificial Analysis** | [artificialanalysis.ai](https://artificialanalysis.ai/) | Independent benchmark index, speed/cost analytics, Coding Agent Index, GDPVal-AA, and AA Intelligence Index |
| **Vals.ai** | [vals.ai](https://vals.ai/) | High-integrity, zero-contamination private benchmark authority (Harvey LAB, Finance Agent, Terminal-Bench) |
| **Chatbot Arena (LMSYS)** | [lmarena.ai](https://lmarena.ai/) | ELO-based human preference ranking |
| **LiveBench** | [livebench.ai](https://livebench.ai/) | Contamination-resistant, monthly refreshed academic benchmark |
| **SWE-Bench Leaderboard** | [swebench.com](https://www.swebench.com/) | Standard agentic coding leaderboard (Lite & Verified splits) |
| **Datacurve (DeepSWE)** | [datacurve.ai](https://datacurve.ai/) | Contamination-free software engineering benchmark suite |
| **Mercor APEX** | [mercor.com](https://mercor.com/) | Real-world white-collar work (APEX-Agents) and systems engineering (APEX-SWE) |
| **Terminal-Bench** | [tbench.ai](https://tbench.ai/) | Command-line environment and systems administration agent evaluations |
| **Cognition (Devin / FrontierCode)** | [cognition.ai](https://www.cognition.ai/) | Production PR mergeability and code quality evaluation |
| **SEAL Leaderboards** | [scale.com/leaderboard](https://scale.com/leaderboard) | Expert-human evaluated enterprise rankings |
| **Open LLM Leaderboard** | [Hugging Face](https://huggingface.co/spaces/open-llm-leaderboard/open_llm_leaderboard) | Open-weight model comparisons |

---

## Further Reading

- 📄 [Artificial Analysis Intelligence Index & Methodology](https://artificialanalysis.ai/methodology) — Comprehensive breakdown of modern multi-pillar composite evaluations
- 📄 [Terminal-Bench: Evaluating AI Agents in Shell Environments](https://arxiv.org/abs/2410.05338) — Terminal-Bench methodology and Linux sandbox evaluation
- 📄 [SWE-bench: Can Language Models Resolve Real-World GitHub Issues?](https://arxiv.org/abs/2310.06770) — SWE-Bench foundation paper
- 📄 [APEX: Benchmarking Long-Horizon Professional & Software Agents](https://arxiv.org/abs/2412.18567) — Mercor APEX benchmark specification
- 📄 [GPQA: A Graduate-Level Google-Proof QA Benchmark](https://arxiv.org/abs/2311.12022) — GPQA paper
- 📄 [Measuring Massive Multitask Language Understanding (MMLU)](https://arxiv.org/abs/2009.03300) — Original MMLU paper
- 📄 [LiveBench: A Challenging, Contamination-Free LLM Benchmark](https://arxiv.org/abs/2406.19314) — LiveBench paper
- 📄 [Chatbot Arena: An Open Platform for Evaluating LLMs by Human Preference](https://arxiv.org/abs/2403.04132) — LMSYS Arena paper
- 📄 [Holistic Evaluation of Language Models (HELM)](https://arxiv.org/abs/2211.09110) — Stanford's comprehensive evaluation framework
- 📝 [Vals.ai: Addressing Benchmark Leakage Through Private Audits](https://vals.ai/) — Evaluating models on private, held-out industry data rooms
- 📝 [How to Read LLM Benchmarks](https://www.latent.space/p/benchmarks) — Practical guide from Latent Space

---

## Guidance

Benchmarks are useful signals, but they do not replace application evals. A model that wins a benchmark can still fail your product if the task, distribution, or risk profile is different.

**The evaluation workflow should be:**

```
Benchmarks → Shortlist → Application Evals → Ship/No-Ship Decision
```

Move to [04. Application Evals →](../04_application_evals/README.md) to learn how to build the eval sets that actually drive shipping decisions.
