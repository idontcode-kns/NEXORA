# 🚀 NEXORA RAG

### Enterprise Agentic Knowledge Intelligence Platform

**NEXORA RAG** is a production-oriented **Agentic Retrieval-Augmented Generation (RAG)** platform engineered for enterprise document intelligence.

Instead of treating RAG as a simple *retrieve → generate* pipeline, NEXORA uses **agentic planning, semantic reranking, safety guardrails, conversational memory, LLM routing, observability, and continuous evaluation** to transform unstructured enterprise knowledge into reliable, context-aware answers.

The platform combines **LangGraph, Portkey, OpenAI, Anthropic, Jina AI, Qdrant, NeMo Guardrails, Pydantic Logfire, LangSmith, FastAPI, Streamlit, Redis, PostgreSQL, Prometheus, and RAGAS** into a scalable AI architecture.

---

## 🧠 What Makes NEXORA RAG Different?

Traditional RAG generally follows:

```text
User Query
    ↓
Retrieve Documents
    ↓
Generate Answer
```

NEXORA introduces an intelligent decision layer:

```text
User Query
    ↓
Safety Validation
    ↓
Intent Detection
    ↓
Agentic Planning
    ↓
Selective Retrieval
    ↓
Vector Search
    ↓
Semantic Reranking
    ↓
Context-Aware Generation
    ↓
Grounded Response
```

The system determines **what the user is asking, whether retrieval is necessary, which information is relevant, and how that information should influence the final response.**

---

# 🏗️ NEXORA Architecture

```mermaid
graph TD

    U((User)) --> UI[Streamlit UI]
    UI --> API[FastAPI /query]

    API --> G{NeMo Guardrails}

    G -->|Blocked| UI
    G -->|Approved| P[LangGraph Planner]

    P -->|Conversational| R[Responder]
    P -->|Technical| V[Vector Retrieval]

    V --> Q[Qdrant Cloud]
    Q --> J[Jina Reranker]

    J --> R

    R --> UI

    R -.-> M[(LangGraph Memory)]

    API --> O[Logfire + LangSmith]
    API --> PM[Prometheus Metrics]
```

---

# ⚡ Core Capabilities

## 1. Agentic Intelligence

**LangGraph** acts as the orchestration layer for the entire reasoning workflow.

The planner can distinguish between:

* Conversational questions
* Technical questions
* Knowledge-intensive queries
* Retrieval-dependent requests
* Unsupported or irrelevant requests

The graph supports:

* Cyclic workflows
* Multi-step reasoning
* Conditional routing
* Stateful conversations
* Conversation memory

**LangGraph MemorySaver** allows NEXORA to preserve context across interactions.

---

# 🛡️ 2. Enterprise AI Guardrails

NEXORA places **NeMo Guardrails before retrieval and generation**.

This prevents potentially unsafe requests from unnecessarily reaching downstream infrastructure.

The guardrail layer can identify and control:

* Prompt injection
* Jailbreak attempts
* Off-topic requests
* Unsafe inputs
* Unsafe outputs
* Manipulative instructions

The philosophy is simple:

```text
Validate First
      ↓
Retrieve Second
      ↓
Generate Last
```

---

# 🚦 3. Intelligent LLM Gateway

All major LLM requests are routed through **Portkey**.

This provides a centralized gateway between the application and model providers.

Benefits include:

* Provider abstraction
* Model flexibility
* Automatic fallback
* Centralized request management
* Production-oriented routing
* Easier provider switching

Primary configuration:

```text
OpenAI GPT-5-mini
```

Fallback provider:

```text
Anthropic
```

Both are managed through the Portkey gateway architecture.

---

# 🔎 4. Enterprise Retrieval Engine

NEXORA uses a two-stage retrieval architecture:

```text
Query
  ↓
Embedding
  ↓
Qdrant Vector Search
  ↓
Candidate Documents
  ↓
Jina Semantic Reranking
  ↓
High-Relevance Context
```

### Qdrant Cloud

Provides scalable vector similarity search.

### Jina AI Reranker

Re-evaluates retrieved passages according to their semantic relevance to the user's question.

This helps reduce the amount of irrelevant information passed into the LLM.

---

# 🧬 5. Intelligent Embeddings

Primary embedding model:

```text
jina-embeddings-v3
```

Vector dimensionality:

```text
1024
```

Local fallback:

```text
mxbai-embed-large-v1
```

This architecture provides resilience when the external embedding service is unavailable.

---

# 📚 6. Local Document Intelligence

NEXORA can process enterprise documents locally before indexing them.

Supported formats include:

* PDF
* HTML
* TXT
* DOCX
* PPTX

The ingestion pipeline follows:

```text
Documents
    ↓
Local Parsing
    ↓
Text Normalization
    ↓
Paragraph-Based Chunking
    ↓
Metadata Creation
    ↓
Embedding Generation
    ↓
Qdrant Indexing
```

Standard document parsing does not require an external OCR service.

---

# 🧠 7. Memory-Aware Conversations

NEXORA is not restricted to isolated questions.

Using **LangGraph MemorySaver**, the system can maintain conversational state.

Example:

```text
User:
Explain Kubernetes work queues.

NEXORA:
Provides explanation.

User:
What about Redis?

NEXORA:
Understands that "what about Redis?" relates
to the previous Kubernetes work-queue discussion.
```

This enables more natural multi-turn enterprise interactions.

---

# 👁️ 8. Full Observability

Complex agentic systems require visibility into every important operation.

NEXORA integrates:

### Pydantic Logfire

For application-level tracing and execution visibility.

### LangSmith

For LLM and LangGraph tracing.

A typical execution can be inspected as:

```text
User Query
    ↓
Guardrail Decision
    ↓
Planner
    ↓
Retriever
    ↓
Qdrant
    ↓
Jina Reranker
    ↓
LLM
    ↓
Final Answer
```

This makes debugging, performance analysis, and production monitoring significantly easier.

---

# 📊 9. Production Metrics

The FastAPI backend exposes:

```text
/metrics
```

for Prometheus-compatible monitoring.

The system can track metrics such as:

* Request volume
* Retrieval count
* Guardrail blocks
* Successful queries
* Failed queries
* Response latency
* Pipeline execution
* Evaluation metrics

---

# 🔐 10. API Security & Rate Limiting

NEXORA supports optional API protection using:

### Bearer Authentication

```text
RAG_API_KEY
```

### Rate Limiting

Supported through:

```text
Upstash Redis
```

with an in-memory fallback for lightweight deployments.

This helps control:

* API abuse
* Excessive traffic
* Uncontrolled model consumption
* Resource utilization

---

# 🧪 11. Built-In Evaluation Framework

NEXORA includes a dedicated evaluation layer powered by **RAGAS**.

The system evaluates multiple dimensions of RAG quality rather than relying solely on manual inspection.

The evaluation suite includes:

* Six RAG-focused metrics
* Custom Tool Correctness evaluation
* Jaccard-based scoring
* Headless evaluation
* Interactive Streamlit evaluation

### CLI

```powershell
python -m evals.run_evals
```

### Interactive Evaluation UI

```powershell
streamlit run evals/app.py
```

---

# 🗂️ Project Structure

```text
NEXORA-RAG/
│
├── app/
│   ├── agents/
│   │   └── nodes/
│   │       ├── planner.py
│   │       ├── retriever.py
│   │       └── responder.py
│   │
│   ├── gateway/
│   │   └── portkey.py
│   │
│   ├── guardrails/
│   │   └── ...
│   │
│   ├── ingestion/
│   │   ├── chunking/
│   │   └── loaders/
│   │
│   ├── services/
│   │   └── retrieval/
│   │
│   ├── config.py
│   └── main.py
│
├── evals/
│   ├── run_evals.py
│   └── app.py
│
├── ui/
│   └── app.py
│
├── processed_data/
│
├── DATA/
│
├── DOCS/
│
├── tests/
│
├── Dockerfile
│
└── requirements.txt
```

---

# 🛠️ Technology Stack

| Layer                    | Technology                   |
| ------------------------ | ---------------------------- |
| Product                  | **NEXORA RAG**               |
| Architecture             | Agentic RAG                  |
| Orchestration            | LangChain + LangGraph        |
| Primary LLM              | OpenAI `gpt-5-mini`          |
| LLM Gateway              | Portkey                      |
| Fallback LLM             | Anthropic                    |
| Guardrails               | NeMo Guardrails              |
| Vector Database          | Qdrant Cloud                 |
| Embeddings               | Jina `jina-embeddings-v3`    |
| Local Embedding Fallback | `mxbai-embed-large-v1`       |
| Reranking                | Jina AI Reranker             |
| Backend                  | FastAPI                      |
| Frontend                 | Streamlit                    |
| Memory                   | LangGraph MemorySaver        |
| Observability            | Pydantic Logfire + LangSmith |
| Metrics                  | Prometheus                   |
| Cache / Rate Limiting    | Upstash Redis                |
| Persistence              | Neon PostgreSQL              |
| Evaluation               | RAGAS + Custom Metrics       |
| Testing                  | Pytest                       |
| Code Quality             | Ruff                         |

---

# 🚀 Getting Started

## 1. Create the Environment

```powershell
python -m venv tenvv
.\tenvv\Scripts\activate

pip install -r requirements.txt
```

---

## 2. Configure Environment Variables

Create a `.env` file:

```env
OPENAI_API_KEY="..."

PORTKEY_API_KEY="..."

JINA_API_KEY="..."

QDRANT_API_KEY="..."
QDRANT_CLUSTER_ENDPOINT="https://your-cluster.cloud.qdrant.io:6333"

NEON_DB_URL="postgresql://user:password@host.neon.tech/enterprise_rag?sslmode=require"

UPSTASH_REDIS_REST_URL="https://your-db.upstash.io"
UPSTASH_REDIS_REST_TOKEN="..."

RAG_API_KEY=""
RATE_LIMIT_PER_MINUTE=20

LOGFIRE_TOKEN="..."

LANGSMITH_API_KEY="..."
LANGSMITH_PROJECT="nexora-rag"
LANGSMITH_TRACING=true
LANGSMITH_ENDPOINT="https://api.smith.langchain.com"

JUDGE_OPENAI_API_KEY="..."

BACKEND_URL="http://localhost:8000"
```

> **Security:** Never commit API keys or credentials to Git. Add `.env` to `.gitignore`.

---

# 📥 3. Ingest Documents

Place enterprise documents inside:

```text
DATA/
```

Run:

```powershell
python -m app.ingestion.processor DATA --wipe
```

The pipeline will:

```text
DATA
 ↓
Parse
 ↓
Chunk
 ↓
Generate Metadata
 ↓
Create Embeddings
 ↓
Index into Qdrant
```

Use `--wipe` when rebuilding the collection from scratch.

Without `--wipe`, documents can be appended to the existing collection.

---

# 🩺 4. Verify Infrastructure

Before launching the application:

```powershell
python -m app.services.health.connection_checker
```

This verifies connectivity to configured external services.

---

# 🖥️ 5. Start FastAPI

```powershell
uvicorn app.main:app --reload --port 8000
```

The primary API endpoint is:

```text
POST /query
```

---

# 🎨 6. Launch NEXORA UI

In another terminal:

```powershell
streamlit run ui/app.py
```

The Streamlit interface provides the conversational experience and can expose retrieval/reasoning information for transparency and debugging.

---

# 🔌 API Example

Send a query to NEXORA:

```powershell
curl -X POST "http://localhost:8000/query" `
  -H "Content-Type: application/json" `
  -d '{"q":"How do I start Redis for a Kubernetes work queue?","thread_id":"user-1"}'
```

Example response:

```json
{
  "question": "How do I start Redis for a Kubernetes work queue?",
  "answer": "...",
  "thought_process": [],
  "status": "success",
  "sources": []
}
```

---

# 🧪 Run the Evaluation Suite

### Headless

```powershell
python -m evals.run_evals
```

### Interactive

```powershell
streamlit run evals/app.py
```

---

# 🧹 Run Code Quality Checks

### Lint

```powershell
ruff check app tests evals
```

### Formatting

```powershell
ruff format --check app tests evals
```

### Tests

```powershell
$env:LOGFIRE_IGNORE_NO_CONFIG=1
pytest tests/
```

---

# 🔄 End-to-End Execution

A technical question moves through NEXORA as:

```text
                         USER
                           │
                           ▼
                  ┌─────────────────┐
                  │   Streamlit UI  │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │     FastAPI     │
                  │     /query      │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │ NeMo Guardrails │
                  └────────┬────────┘
                           │
                       APPROVED
                           │
                           ▼
                  ┌─────────────────┐
                  │ LangGraph       │
                  │ Planner         │
                  └────────┬────────┘
                           │
                  ┌────────┴────────┐
                  │                 │
           Conversational       Technical
                  │                 │
                  ▼                 ▼
              Responder       Qdrant Search
                                    │
                                    ▼
                              Jina Reranker
                                    │
                                    ▼
                                Responder
                                    │
                                    ▼
                           Grounded Response
                                    │
                                    ▼
                              Streamlit UI
```

---

# 🎯 Engineering Principles

NEXORA is built around five fundamental principles:

### **1. Plan Before Retrieving**

Not every user query requires vector search.

### **2. Retrieve Broadly, Rank Precisely**

Qdrant identifies candidate documents while Jina determines semantic relevance.

### **3. Guard Before Processing**

Unsafe or irrelevant requests should be filtered before expensive downstream operations.

### **4. Observe Everything That Matters**

Agentic workflows need traceability across every critical execution stage.

### **5. Evaluate Continuously**

A production RAG system should be measured—not simply demonstrated.

---

# 🌐 Production Architecture

```text
                    ┌────────────────────┐
                    │    NEXORA UI       │
                    │    Streamlit       │
                    └─────────┬──────────┘
                              │
                    ┌─────────▼──────────┐
                    │      FastAPI       │
                    └─────────┬──────────┘
                              │
                 ┌────────────▼────────────┐
                 │       LangGraph         │
                 │   Agent Orchestration   │
                 └────────────┬────────────┘
                              │
             ┌────────────────┼────────────────┐
             │                │                │
             ▼                ▼                ▼
        Guardrails         Memory         Observability
        NeMo              LangGraph       Logfire/LangSmith
             │
             ▼
       Retrieval Layer
             │
       ┌─────┴─────┐
       ▼           ▼
    Qdrant       Jina AI
    Search       Reranker
       │           │
       └─────┬─────┘
             ▼
       Portkey Gateway
             │
       ┌─────┴─────┐
       ▼           ▼
     OpenAI     Anthropic
```

---

# 🚀 NEXORA RAG — Project Vision

**NEXORA RAG** is designed to demonstrate what an enterprise RAG system can become when retrieval is combined with **agentic decision-making, semantic intelligence, security, memory, observability, evaluation, and scalable infrastructure**.

The objective is not simply to build another chatbot.

It is to build an AI knowledge system capable of deciding:

> **What does the user need?**
> **Is retrieval necessary?**
> **Which information is actually relevant?**
> **Is the request safe?**
> **Which model should handle it?**
> **Can the answer be grounded in trusted evidence?**
> **How can the entire process be measured and improved?**

That is the foundation of **NEXORA RAG — Enterprise Agentic Knowledge Intelligence.**

---

### ⭐ NEXORA RAG

**Agentic Intelligence • Enterprise Retrieval • Semantic Reranking • AI Safety • LLM Routing • Observability • Continuous Evaluation**
