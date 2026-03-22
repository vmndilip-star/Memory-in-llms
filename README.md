# 🧠 Memory in LLMs

A hands-on project exploring memory mechanisms in Large Language Models using **LangChain**, **LangGraph**, and **PostgreSQL**. Each notebook progressively builds on the previous one, starting from basic stateless chat to fully persistent long-term memory with semantic search.

## 📌 About

LLMs are stateless by default — they don't remember anything between conversations. This project walks through every major memory strategy to make LLMs context-aware, from simple in-memory checkpointing to production-ready PostgreSQL-backed long-term memory with automatic memory extraction.

## 📂 Project Structure

| # | Notebook | What You'll Learn |
|---|----------|-------------------|
| 1 | `1_stm.ipynb` | **STM Basics** — Build a LangGraph chatbot with `InMemorySaver` checkpointer. Understand how `thread_id` isolates conversations and why the bot forgets across threads. |
| 2 | `2_stm_persistence.ipynb` | **STM with PostgreSQL** — Replace in-memory checkpointing with `PostgresSaver` so conversation history survives app restarts. Uses Docker for Postgres setup. |
| 3 | `3_stm_trimming.ipynb` | **STM Trimming** — Use `trim_messages` with a token budget (`MAX_TOKENS=150`) to keep only the most recent messages that fit, preventing context window overflow. |
| 4 | `4_stm_deletion.ipynb` | **STM Deletion** — Add a `cleanup` node that uses `RemoveMessage` to delete the oldest 6 messages once the conversation exceeds 10 messages. |
| 5 | `5_stm_summarization.ipynb` | **STM Summarization** — Instead of deleting old messages, summarize them into a rolling summary. Keeps the last 2 messages verbatim while compressing everything else. |
| 6 | `6_ltm_basics.ipynb` | **LTM Basics** — Introduction to `InMemoryStore` for long-term memory. Learn namespaces, `put`/`get`/`search` operations, and semantic search with OpenAI embeddings. |
| 7 | `7_ltm_implementation.ipynb` | **LTM Implementation** — Full chatbot with read + write long-term memory. A `remember` node uses structured output (`MemoryDecision`) to automatically extract and store new user facts. |
| 8 | `8_ltm_postgres.ipynb` | **LTM with PostgreSQL** — Production-ready setup using `PostgresStore` for persistent long-term memory. Memories survive restarts and are stored per-user in Postgres. |

### Supporting Files

| File | Purpose |
|------|---------|
| `docker-compose.yml` | Spins up a PostgreSQL 16 container on port `5442` (used by notebooks 2 and 8) |

## 🛠️ Tech Stack

- **Python 3.11**
- **LangGraph** — Graph-based orchestration for LLM workflows
- **LangChain** — LLM framework (ChatOpenAI, message utilities)
- **OpenAI API** — GPT-4o-mini for chat and embeddings
- **PostgreSQL 16** — Persistent storage for checkpoints and long-term memory
- **Docker** — Container setup for PostgreSQL
- **Pydantic** — Structured output for memory extraction

## 🚀 Getting Started

### Prerequisites

- [Anaconda/Miniconda](https://docs.conda.io/en/latest/miniconda.html)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- An [OpenAI API key](https://platform.openai.com/api-keys)

### Setup

1. **Clone the repo:**
   ```bash
   git clone https://github.com/vmndilip-star/memory-in-llms.git
   cd memory-in-llms
   ```

2. **Create and activate the conda environment:**
   ```bash
   conda create -n langchain_env python=3.11
   conda activate langchain_env
   ```

3. **Install dependencies:**
   ```bash
   pip install langchain langgraph langchain-openai langchain-community langgraph-checkpoint-postgres "psycopg[binary,pool]" python-dotenv pydantic
   ```

4. **Set up your API key:**
   ```bash
   # Create a .env file in the project root
   echo "OPENAI_API_KEY=your_api_key_here" > .env
   ```

5. **Start PostgreSQL** (needed for notebooks 2 and 8):
   ```bash
   docker-compose up -d
   ```

6. **Run the notebooks in order** starting from `1_stm.ipynb`.

## 🗺️ Learning Path

```
Stateless Chat
    │
    ▼
┌─────────────────────────────────┐
│  SHORT-TERM MEMORY (STM)        │
│                                 │
│  1. Basics (InMemorySaver)      │
│  2. Persistence (PostgreSQL)    │
│  3. Trimming (token budget)     │
│  4. Deletion (RemoveMessage)    │
│  5. Summarization (rolling)     │
└────────────────┬────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│  LONG-TERM MEMORY (LTM)        │
│                                 │
│  6. Basics (InMemoryStore)      │
│  7. Implementation (auto-write) │
│  8. PostgreSQL (persistent)     │
└─────────────────────────────────┘
```

### Key Concepts

**Short-Term Memory (STM)** — Remembering within a conversation session using checkpointers and thread IDs. Strategies include trimming messages by token count, deleting old messages, and summarizing past context.

**Long-Term Memory (LTM)** — Remembering across sessions using a separate store. Supports namespaced storage per user, semantic search with embeddings, and automatic memory extraction from conversations using structured LLM output.

## 📝 Notes

- Notebooks 1–5 focus on STM and can run without Docker (except notebook 2)
- Notebooks 2, 8 require PostgreSQL — run `docker-compose up -d` first
- The PostgreSQL container maps to port **5442** (not the default 5432) to avoid conflicts
- All notebooks use `python-dotenv` to load the OpenAI API key from a `.env` file

## 🙏 Acknowledgements

Inspired by [CampusX](https://github.com/campusx-official) tutorials on LLMs and LangChain.

