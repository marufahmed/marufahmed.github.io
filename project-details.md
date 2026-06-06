# Project Deep-Dives

Expanded detail for each portfolio card. Each section follows a motivated sequence:
**the problem → what it solves → the hard parts → how we beat them.** A shared engineering
philosophy runs through all four — see *Lean by Design* at the end.

---

## 01 · Automated RMG QA/QC Platform (Argus)

**The problem.** Garment quality control on an RMG line is manual and subjective — inspectors
measure finished pieces by hand against a spec sheet. It's slow, it varies person to person,
and at production volume you can only ever sample a fraction of what you ship.

**What it solves.** Argus is the world's first AI-powered garment measurement system built on
**Structured Light 3D sensing**: it captures a garment's true geometry and reports measurements
automatically, reaching **93.5% accuracy compared to human inspectors** — objective, repeatable
QC that can run inline instead of on a sampling table.

**Challenges & how we overcame them.**

- *Real-time inspection can't wait on slow inference.* The first pipeline took 4.5s per piece —
  too slow for the line. We optimized it with **Nvidia Triton + TensorRT**, cutting inference
  time **82% (4.5s → 0.8s)** so measurement keeps pace with production.
- *Tape-measure precision from a sensor is genuinely hard.* We developed novel finetuning methods
  using **vector embeddings with positional encoding** to reach **subpixel accuracy** — the
  difference between a demo and a measurement a factory will actually trust.
- *It had to convince the industry, not just a benchmark.* The system was validated by **BGMEA
  board members** and drew **10+ unit backorders before launch** — proof the speed and accuracy
  held up against real QC expectations, and an early example of the edge-first instinct that runs
  through later work.

---

## 02 · AI-Native Special-Education Operations Platform (Bloomridge Springs)

**The problem.** A special-needs school and therapy center ran its week on whiteboards and
group chats. Matching dozens of students to the right therapists — around availability,
time-off, room limits and each child's preferred hours — ate **6–7 hours of an admin's day**,
every day. On top of that sat the clinical paperwork: IEPs, SMART goals, progress notes and
monthly reports, all written by hand.

**What it solves.** One multi-tenant platform that plans the week, writes the first draft of
the paperwork, and keeps parents informed automatically:

- **Scheduling in 2–3 seconds** instead of 6–7 hours, with WhatsApp session notifications
  sent the moment a plan is published.
- **Agentic documentation** that drafts IEPs and goals from real assessment evidence,
  removing hours of clinical writing each week.
- A natural-language assistant staff can simply *ask* — "who's unbooked Thursday?",
  "draft Aarav's next goal" — instead of clicking through screens.

**Challenges & how we overcame them.**

- *Scheduling is a real constraint problem, not a sort.* A naive greedy matcher produced
  illegal or lopsided weeks. We modelled it as a true CSP on **Google OR-Tools CP-SAT**:
  availability, time-off, room capacity and no-double-booking as hard constraints, then a
  weighted objective that maximizes booked sessions *and* rewards each student's preferred
  slots — turning a daily chore into a solved optimization.
- *An AI agent with write access to clinical records is dangerous.* We layered defense in
  depth: role-based tool access (each tool declares which roles may call it) plus a
  **human-in-the-loop approval gate** — any tool that mutates clinical data parks in a pending
  state until an admin releases it. The agent is helpful, never unsupervised.
- *AI-written clinical docs can't be free-form.* Every IEP and goal the LLM produces is
  validated against a strict Pydantic schema and persisted as a **versioned draft** — never
  auto-published — so clinicians review structured, auditable output.

---

## 03 · SwiftFit — 3D Body Measurement from Photos

**The problem.** Online clothing returns are overwhelmingly a *sizing* problem. Existing
"measure yourself" flows ask people to wrap a tape measure correctly — which they don't — or
need depth sensors most phones lack.

**What it solves.** Upload a few ordinary photos, enter your height, and SwiftFit reconstructs
a metric-accurate **3D body model**, reads real circumferences and lengths off it, and maps
those to the correct size for any retailer's chart.

**Challenges & how we overcame them.**

- *Single-image body reconstruction is unstable across angles.* We extended the ECON pipeline
  into **MExECON**, fusing multiple views through **Joint Multi-view Body Optimization (JMBO)**
  so one consistent SMPL-X body explains every photo — far more robust than per-image guesses.
- *A parametric body is smooth; real bodies have surface detail.* We predict clothed surface
  normals and recover fine geometry with **bilateral normal integration** and Poisson fusion,
  orchestrating PIXIE, PyMAF-X and Sapiens under a single CUDA inference graph.
- *Heavy GPU inference feels broken if the user just stares at a spinner.* The FastAPI backend
  streams live pipeline progress over WebSocket into an interactive **React Three Fiber**
  viewer — the user watches their model build, step by step.
- **Where it stands:** **83.4% reconstruction accuracy** today, with active research pushing
  it higher.

---

## 04 · SwiftWallet — AI Finance Intelligence

**The problem.** Personal budgeting dies on data entry. Nobody types in receipts, so spending
data is always stale and incomplete — and bank statements arrive as messy PDFs.

**What it solves.** Snap a receipt or drop in a statement; SwiftWallet turns it into clean,
categorized transactions with **zero manual entry** — and lets you ask questions about your
money in plain language.

**Challenges & how we overcame them.**

- *OCR on crumpled, low-light receipts is the make-or-break step.* We run a dedicated
  GPU vision-OCR model and tuned the pipeline to **99.2% text-extraction accuracy** — the
  foundation everything downstream depends on.
- *Raw OCR text is not data.* A **Groq-hosted Llama-4 LLM** parses merchant, date, totals and
  line items into a strict schema and classifies each item against the *user's own* categories —
  structured output, not a wall of text.
- *AI parsing is too slow to block an upload.* The full *image → OCR → categorize → expense*
  flow runs asynchronously on **Celery workers** (monitored via Flower), so the UI stays
  instant while inference happens in the background.
- *Querying your finances should feel like a conversation.* An agentic chat layer with
  function-calling tools answers "how much did I spend on groceries last month?" directly
  against the user's data.

---

## 05 · Real-Time Bengali Voice Agent

**The problem.** Booking a doctor's appointment by phone means hold music and clinic hours.
Voice assistants that *do* exist are English-first and stumble on Bengali — and most feel
laggy and robotic, with awkward turn-taking.

**What it solves.** A fully voice-native assistant that books, reschedules and cancels
appointments **entirely in Bengali**, reasoning about symptoms to route patients to the right
specialist — conversational enough to feel like talking to a receptionist.

**Challenges & how we overcame them.**

- *Latency kills voice UX.* We built the loop as isolated microservices — **GPU faster-whisper
  STT → Groq tool-calling LLM → edge-TTS** — and tuned it to process audio at **219× real-time
  speed**, so spoken replies feel near-instant.
- *Real conversations interrupt.* VAD-gated segmentation plus **barge-in / interrupt handling**
  over bidirectional WebSockets let the user cut in mid-sentence, like a real exchange.
- *An LLM shouldn't hallucinate which doctor treats what.* We ground medical reasoning in a
  Bengali **RAG knowledge base** (ChromaDB + multilingual embeddings) that maps symptoms to
  specialists, then drive six concrete booking tools across 12 doctors with Redis-backed
  session memory.
- *AI "magic" is hard to trust.* A live React dashboard renders the entire agent trace —
  transcription, reasoning, tool calls, schedule updates — in real time, making every decision
  visible.

---

## 06 · Melody — RAG Chat Assistant

**The problem.** Enterprise knowledge is scattered — half-answers live in Confluence, Jira
tickets, MongoDB records and a graveyard of PDFs and DOCX files. Employees waste hours hunting
across tools for something a colleague already wrote down.

**What it solves.** A single RAG chat assistant that ingests all of those sources and answers
questions in natural language with grounded, cited context — one place to ask, instead of five
to search.

**Challenges & how we overcame them.**

- *A single retriever misses too much.* Dense vector search alone returned plausible-but-wrong
  passages on enterprise jargon and ticket IDs. We combined **TF-IDF + BM-25 + cosine
  similarity** into a hybrid retriever, lifting retrieval performance **35%** — lexical signals
  catch exact terms, semantic search catches intent.
- *Many source formats, one pipeline.* Confluence, Jira, MongoDB, PDF and DOCX each have their
  own structure; we normalized them into a unified chunked-and-embedded corpus so the retriever
  treats them uniformly.
- *Shipping reliably at enterprise cadence.* A full CI/CD flow on **GitHub Actions** with
  MLflow experiment tracking and K8s deployment cut build time **40%**, making model and
  pipeline iterations routine rather than risky.

---

## 07 · GPU-on-Demand MLOps Platform

**The problem.** ML teams burn money on always-on GPU instances that sit idle between training
runs — and provisioning the right GPU by hand, across providers, is slow and error-prone.

**What it solves.** Infrastructure that spins GPUs up **on demand** and tears them down when
the job is done — provisioned as code, across multiple clouds — so teams pay for compute only
while they use it.

**Challenges & how we overcame them.**

- *Idle GPUs are the cost sink.* We replaced standing instances with **Terraform**-provisioned,
  on-demand GPUs integrated with Lambdalabs, cutting GPU spend **95%**.
- *No single provider has capacity or price locked down.* We built multi-cloud SDK services
  that abstract provisioning across **Lambdalabs and Runpod**, so workloads land wherever
  capacity and cost are best — no vendor lock-in.
- *Provisioning has to be repeatable, not artisanal.* Infrastructure-as-code with K8s + Helm
  and GitLab CI turned GPU setup into a versioned, automated step instead of a manual ritual.

---

## 08 · Bengali OCR System

**The problem.** Bengali OCR lagged far behind English — a script with complex conjuncts and
ligatures, and almost no large-scale, high-quality training data to learn from.

**What it solves.** A production OCR system, deployed at national scale (`ocr.bangla.gov.bd`),
that reads Bengali documents accurately and fast.

**Challenges & how we overcame them.**

- *The data didn't exist.* We curated the **largest Bengali OCR dataset** to date — the real
  unlock, since model quality is bounded by data quality for a low-resource script.
- *Accuracy and speed usually trade off.* Through R&D on detection/recognition architectures we
  pushed processing **32% faster** while holding **system errors under 1%** — production-grade
  on both axes.
- *Research has to survive production.* CV and NLP models were hardened and deployed behind a
  public government service, where reliability is non-negotiable.

---

## 09 · Smart Pavement Crack Detection

**The problem.** Road surveying for cracks is manual, slow and subjective — inspectors can't
cover every mile, and assessments vary person to person.

**What it solves.** An autonomous vehicle platform that detects pavement cracks in real time
as it drives, producing consistent, automated road-condition data.

**Challenges & how we overcame them.**

- *Detection had to run on the move.* An **SSD-based** detection network hit **96% accuracy**
  while staying fast enough for real-time inference.
- *Inference at the edge, not in a data center.* We deployed on **Nvidia Jetson TX2** mounted
  on the vehicle, doing collection and inference on-board — the same edge-first instinct behind
  later work like Argus.
- *Rigorous enough to fund.* The work was conducted as **NSF REU Fellowship** research at Boise
  State University, held to an academic standard of evaluation.

---

## Lean by Design — A Shared Philosophy

One conviction runs through every project above, from a research SSD model on a Jetson to a
multi-tenant clinical platform: **earn the result with the leanest thing that works — don't
buy it with abstraction, idle cost, or hype.**

- **No LangChain, no heavyweight agent frameworks.** For the agentic systems I call the model
  provider (Groq) directly and define tools as plain function schemas. Frameworks like LangChain
  add layers of abstraction, hidden prompt bloat and dependency weight that I'd only have to
  fight later. The agent loop is small enough to read top to bottom.
- **Token efficiency is a first-class constraint.** Context is built deliberately — only the
  fields a tool actually needs, scoped per tenant and per user — rather than dumping records
  into the prompt. Fewer tokens means faster, cheaper, more reliable agents.
- **Right-sized models and tools for each job.** Fast Groq-hosted models for interactive agents,
  lightweight multilingual embeddings for RAG, local ChromaDB instead of a managed vector
  service, hybrid lexical+semantic retrieval instead of a bigger model — capability matched to
  need, not to hype.
- **Pay only for what runs.** The same instinct shows up in infrastructure: GPUs provisioned
  on demand rather than left idle (95% cost cut), inference pushed to the edge (Jetson, Raspberry
  Pi) instead of round-tripping to a data center, and everything reproducible as code.
- **Quality at the source.** Whether it's curating the largest Bengali OCR dataset or validating
  every AI-drafted clinical document against a strict schema, I'd rather fix correctness at the
  input than paper over it downstream.
- **The result:** systems that are auditable, cheap to run, fast, and free of the dependency
  churn that comes with chasing trendy libraries — efficiency as a design value, not an
  afterthought.
