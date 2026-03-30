# 12 — Emerging Technologies: GenAI, AI/ML Awareness

---

> **TCS Digital specifically looks for awareness of emerging technologies. You don't need to be an expert, but you must demonstrate that you understand the landscape and can discuss these topics intelligently.**

---

## 12.1 AI vs ML vs Deep Learning vs GenAI

```
┌─────────────────────────────────────────────────────┐
│              Artificial Intelligence (AI)            │
│   Broad field: machines performing intelligent tasks │
│                                                     │
│   ┌─────────────────────────────────────────────┐   │
│   │          Machine Learning (ML)              │   │
│   │  Learning from data without explicit coding  │   │
│   │                                             │   │
│   │   ┌─────────────────────────────────────┐   │   │
│   │   │       Deep Learning (DL)            │   │   │
│   │   │  Neural networks with many layers   │   │   │
│   │   │                                     │   │   │
│   │   │   ┌─────────────────────────────┐   │   │   │
│   │   │   │    Generative AI (GenAI)    │   │   │   │
│   │   │   │  Creates new content        │   │   │   │
│   │   │   │  (text, images, code)       │   │   │   │
│   │   │   └─────────────────────────────┘   │   │   │
│   │   └─────────────────────────────────────┘   │   │
│   └─────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────┘
```

### Key Distinctions

| Term | What It Does | Example |
|------|-------------|---------|
| **AI** | Any machine exhibiting intelligent behavior | Chess engine, spam filter, Siri |
| **ML** | Algorithms that learn patterns from data | Recommendation systems (Netflix), fraud detection |
| **DL** | Neural networks with multiple hidden layers | Image recognition (CNNs), speech recognition |
| **GenAI** | Creates NEW content based on learned patterns | ChatGPT (text), DALL-E (images), GitHub Copilot (code) |

---

## 12.2 Generative AI & Large Language Models (LLMs)

### How LLMs Work (Simplified)

LLMs predict the **next token** based on the context of all preceding tokens. They're trained on massive text datasets (essentially the internet) and learn statistical patterns of language.

```
Input: "The capital of France is"
LLM predicts: "Paris" (highest probability next token)
```

### Key Concepts

| Concept | Definition | Why It Matters |
|---------|-----------|---------------|
| **Transformer Architecture** | Neural network design using "self-attention" mechanism — processes all words simultaneously, not sequentially | Foundation of ALL modern LLMs (GPT, BERT, etc.) |
| **Prompt Engineering** | Crafting effective inputs to get desired outputs from LLMs | Directly applicable — you use this daily |
| **RAG (Retrieval-Augmented Generation)** | LLM retrieves relevant documents from a database before generating answers | Reduces hallucinations; enables LLMs to use private data |
| **Fine-Tuning** | Further training an LLM on domain-specific data | Medical ChatGPT, legal document AI |
| **Hallucination** | LLM generates plausible but factually incorrect information | Major challenge; why human oversight is critical |

### Prompt Engineering Strategies

| Strategy | What It Is | Example |
|----------|-----------|---------|
| **Zero-Shot** | Ask directly without examples | "Classify this email as spam or not spam" |
| **Few-Shot** | Provide examples before asking | "Email 1 → Spam. Email 2 → Not Spam. Email 3 → ?" |
| **Chain-of-Thought** | Ask the model to reason step-by-step | "Think step by step: What is 23 × 17?" |

---

## 12.3 Big Data Concepts

### The 5 V's of Big Data

| V | Definition | Your Project Context |
|---|-----------|---------------------|
| **Volume** | Amount of data | 8M+ records, ~16GB raw |
| **Velocity** | Speed of data generation | LA crime data updated daily |
| **Variety** | Different data types | Structured (CSV, SQL), semi-structured (JSON APIs) |
| **Veracity** | Data quality/trustworthiness | 15% null locations, inconsistent dates |
| **Value** | Business insights derived | Crime trend analysis, resource allocation |

### Where Your Tools Fit in the Big Data Pipeline

```
Data Sources → Ingestion → Processing → Storage → Analysis → Visualization
                  │            │           │          │           │
              Python       Pandas      MySQL      SQL        Power BI
              (your ETL)   (transform) (database) (queries)  (dashboards)
              
For enterprise scale, this becomes:
              Kafka →     Spark →    Snowflake → dbt →     Tableau
```

**Q: "Is 8 million records 'Big Data'?"**
> "Not in the distributed computing sense — true Big Data (petabytes) requires distributed systems like Hadoop or Spark. 8 million records is 'medium data' that fits on a single machine with proper memory management. However, the principles I applied — chunked processing, batch loading, memory-efficient operations — are the same principles used at Big Data scale, just without the distributed infrastructure."

---

## 12.4 Blockchain (Surface Level)

**What**: A decentralized, distributed ledger that records transactions across many computers in a way that makes records tamper-resistant.

**Key Properties**:
- **Decentralized** — No single authority controls it
- **Immutable** — Once recorded, data can't be altered
- **Transparent** — All participants can see all transactions
- **Consensus** — Network agrees on the validity of transactions

**Use Cases**: Cryptocurrency, supply chain tracking, smart contracts, digital identity

---

## 12.5 IoT (Internet of Things)

**What**: Network of physical devices embedded with sensors, software, and connectivity to exchange data.

**Examples**: Smart thermostats, fitness trackers, industrial sensors, connected vehicles.

**Connection to your skills**: IoT devices generate massive data streams that need ETL pipelines (like yours) to process, store in databases, and visualize on dashboards.

---

## 12.6 How to Answer AI Tool Questions

**Q: "Do you use AI tools like ChatGPT for coding?"**
> "Yes — and I believe anyone claiming otherwise in 2026 isn't being honest. I use AI tools for **three specific purposes**:
> 1. **Boilerplate code generation** — repetitive patterns, config files
> 2. **Syntax reference** — when I need to recall a Pandas function's exact parameters
> 3. **Debugging hints** — when I'm stuck on an error, I'll describe the problem and evaluate the suggested fix
>
> But I **always audit the output** for correctness, security (no hardcoded credentials), edge cases, and schema fit. For my Crime Data pipeline, the architecture decisions — chunked processing, MySQL schema design, DAX measures — are entirely mine. I can explain every design choice because I made them."

**Q: "What's the difference between using AI as a tool vs. depending on AI?"**
> "Using AI as a tool means you understand what you're asking it to do and can evaluate whether the output is correct. Depending on AI means you can't write or understand the code without it. I fall in the first category — I use AI to accelerate, not to replace my thinking."

---

## 12.7 TCS's Position on Emerging Tech

TCS has significant investments in:

- **TCS AI.Cloud** — AI and cloud solutions for enterprises
- **TCS Pace Ports** — Innovation labs for emerging tech (IoT, Blockchain, AI)
- **TCS BaNCS** — Financial technology platform
- **Industry Cloud Solutions** — Sector-specific cloud platforms

**Why this matters**: When asked "Why TCS?", mentioning their tech investments shows you've done your research:
> "TCS's investment in AI.Cloud and their Pace Port innovation labs tells me they're not just a services company — they're building platforms. For someone in data analytics, working on AI-driven analytics solutions at TCS's scale is exactly the kind of growth I'm looking for."

---

*Next: [13_POWERBI_ANALYTICS.md](./13_POWERBI_ANALYTICS.md) — Power BI, DAX & Data Visualization*
