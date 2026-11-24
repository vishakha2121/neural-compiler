# 🧠 Neural Compiler — AI Compiler for Large Language Models

> **Transform prompts and multi-step workflows into optimized execution graphs — powered by a real compiler pipeline, multi-provider LLM adapters, and a beautiful React dashboard.**

[![Python](https://img.shields.io/badge/Python-3.11+-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.100+-009688?logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![React](https://img.shields.io/badge/React-18+-61DAFB?logo=react&logoColor=black)](https://react.dev/)
[![Vite](https://img.shields.io/badge/Vite-5+-646CFF?logo=vite&logoColor=white)](https://vitejs.dev/)
[![TailwindCSS](https://img.shields.io/badge/TailwindCSS-3+-38BDF8?logo=tailwindcss&logoColor=white)](https://tailwindcss.com/)
[![SQLite](https://img.shields.io/badge/SQLite-3+-003B57?logo=sqlite&logoColor=white)](https://sqlite.org/)
[![Gemini](https://img.shields.io/badge/Gemini-API-4285F4?logo=google&logoColor=white)](https://ai.google.dev/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](./LICENSE)

---

## 📌 Executive Summary

**Neural Compiler** is a full-stack, AI-native compiler designed to take high-level prompts and multi-step workflows and convert them into **optimized, provider-agnostic execution graphs**. 

Just like a traditional compiler (like GCC or LLVM) transforms human-readable source code into efficient machine code, this project transforms human-readable prompt workflows (DSL) into a structured **Intermediate Representation (IR)** — a Directed Acyclic Graph (DAG) — which is then optimized, parallelized, and executed across multiple Large Language Model (LLM) providers.

**Why does this project exist?**
Modern AI applications are rarely a single prompt. They involve multi-step reasoning chains, conditional branching, parallel calls, and cost/latency tradeoffs. Developers hand-code these workflows with fragile chains of `await` calls. There is no compiler, no optimizer, and no observability.

Neural Compiler solves this by bringing **classic compiler theory** to **modern AI workloads**. It demonstrates:
- Compiler construction (Lexer, Parser, Semantic Analyzer, IR, Optimizer)
- Graph theory (DAG optimization, topological sort, parallelization detection)
- Multi-provider abstraction (Adapter pattern for Gemini, OpenAI, Claude)
- Real-time systems (WebSockets, streaming execution logs)
- Full-stack engineering (FastAPI backend, React frontend, SQLite DB)
- Beautiful modern UI/UX (React Flow, TailwindCSS, Framer Motion)

> **Note:** This is a **practice/learning project**, explicitly built for portfolio and educational purposes. It is **not intended for enterprise or production deployment** and runs entirely on CPU (cloud API dependent).

---

## 🏗️ System Architecture & Compiler Pipeline

The project follows the classic compiler design architecture, elegantly adapted for LLMs.

```text
[ USER WORKFLOW DSL (JSON/YAML) ]
               │
               ▼
┌───────────────────────────────────────┐
│ 1. LEXER                              │ -> Converts raw text/JSON into structured tokens.
└──────────────────┬────────────────────┘
                   ▼
┌───────────────────────────────────────┐
│ 2. PARSER                             │ -> Builds an Abstract Syntax Tree (AST) from tokens.
└──────────────────┬────────────────────┘
                   ▼
┌───────────────────────────────────────┐
│ 3. SEMANTIC ANALYZER                  │ -> Validates dependencies, detects cycles, checks types.
└──────────────────┬────────────────────┘
                   ▼
┌───────────────────────────────────────┐
│ 4. IR BUILDER (Intermediate Rep.)     │ -> Converts AST into a Graph-based DAG representation.
└──────────────────┬────────────────────┘
                   ▼
┌───────────────────────────────────────┐
│ 5. OPTIMIZER ENGINE                   │ -> Performs graph-level optimizations:
│    • Dead Code Elimination            │    (Removes unused nodes)
│    • Parallelizer                     │    (Groups independent nodes)
│    • Common Subexpression (CSE)       │    (Caches identical prompts)
│    • Provider Selector                │    (Assigns best provider)
└──────────────────┬────────────────────┘
                   ▼
┌───────────────────────────────────────┐
│ 6. EXECUTOR (DAG Traversal)           │ -> Runs the graph using topological sort & async tasks.
└──────────────────┬────────────────────┘
                   ▼
       ┌───────────┼───────────┐
       ▼           ▼           ▼
 [ Gemini ]   [ OpenAI ]  [ Claude ]
 (API Call)   (Mock API)  (Mock API)
       │           │           │
       └───────────┼───────────┘
                   ▼
┌───────────────────────────────────────┐
│ 7. WEBSOCKET STREAMER                 │ -> Pushes live execution logs to the React Frontend.
└───────────────────────────────────────┘


---

### ⬇️ Turant Iske Niche (Part 2) Copy Karna Shuru Kar ⬇️

```markdown
---

## 🗄️ Database Schema (SQLite)

The database is structured to support workflow persistence, execution tracking, and real-time logging.

### 1. `workflows`
Stores the saved DSL definitions.
- `id` (PK, UUID)
- `name` (String)
- `description` (Text)
- `dsl_content` (Text / JSON)
- `created_at` (Timestamp)
- `updated_at` (Timestamp)

### 2. `executions`
Represents a single run of a compiled workflow.
- `id` (PK, UUID)
- `workflow_id` (FK -> workflows.id)
- `status` (Enum: pending, running, completed, failed)
- `total_tokens` (Integer)
- `total_cost` (Float)
- `started_at` (Timestamp)
- `completed_at` (Timestamp)

### 3. `execution_nodes`
Tracks the state of every node in the graph during a specific execution.
- `id` (PK, UUID)
- `execution_id` (FK -> executions.id)
- `node_id` (String - ID from the IR graph)
- `status` (Enum: pending, running, completed, failed)
- `provider_used` (String)
- `prompt_sent` (Text)
- `output_received` (Text)
- `tokens_used` (Integer)
- `latency_ms` (Integer)

### 4. `provider_configs`
Stores API keys and toggles for LLM providers.
- `id` (PK, Integer)
- `provider_name` (Enum: gemini, openai, claude)
- `api_key_encrypted` (String - stored locally)
- `is_active` (Boolean)
- `default_model` (String)

---

## 🔌 API Endpoints Reference

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/compile` | Accepts DSL. Returns AST, IR, and Optimized Graph. |
| `POST` | `/api/execute` | Accepts an optimized graph. Starts async execution. |
| `GET` | `/api/workflows` | List all saved workflows. |
| `POST` | `/api/workflows` | Save a new workflow. |
| `GET` | `/api/executions` | List past executions. |
| `GET` | `/api/executions/{id}`| Get detailed execution metadata and node states. |
| `GET` | `/api/providers` | List configured providers. |
| `POST` | `/api/providers` | Update/Create provider configs. |
| `WS` | `/ws/execution/{id}` | WebSocket for live execution logs. |

---

## 📝 The Workflow DSL (Domain Specific Language)

The DSL is designed to be easily readable by humans and easily parsable by the compiler. It is written in JSON format.

**Example: A Chain Workflow (Write -> Summarize -> Translate)**

```json
{
  "workflow_name": "content_pipeline",
  "nodes": [
    {
      "id": "input_1",
      "type": "input",
      "data": { "text": "The future of AI in healthcare" }
    },
    {
      "id": "writer",
      "type": "llm",
      "provider": "gemini",
      "dependencies": ["input_1"],
      "prompt": "Write a 200-word article about: {{input_1}}"
    },
    {
      "id": "summarizer",
      "type": "llm",
      "provider": "gemini",
      "dependencies": ["writer"],
      "prompt": "Summarize this into 3 bullet points: {{writer}}"
    },
    {
      "id": "translator",
      "type": "llm",
      "provider": "openai", 
      "dependencies": ["summarizer"],
      "prompt": "Translate to Hindi: {{summarizer}}"
    }
  ]
}


git clone https://github.com/vishakha2121/neural-compiler.git
cd neural-compiler


cd backend
python -m venv .venv
# Windows: 
.venv\Scripts\activate
# Mac/Linux:
source .venv/bin/activate

pip install -r requirements.txt

# Setup Environment variables
copy .env.example .env
# Edit .env and add your GEMINI_API_KEY


cd frontend
npm install

# Setup Environment variables
copy .env.example .env
# Ensure VITE_API_URL=http://localhost:8000

npm run dev