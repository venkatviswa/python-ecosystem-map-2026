# Python 2026 Cheat Sheet

*One-page companion to the [full Python Ecosystem Map](README.md). Scan → pick a default → name it in your prompt.*

**Python versions:** default to **3.12 / 3.13** · **3.14** for new projects once deps have wheels · **avoid 3.9** (EOL Oct 2025).
**Maturity in table:** untagged = stable default · **(E)** emerging *(pin versions, read changelogs)* · **(X)** experimental · **(V)** vendor · **(O)** opinionated.

---

## System-prompt primer — drop into Cursor / Claude Projects / Codex

> **Use a modern 2026 Python stack. Prefer stable, typed, async-capable libraries; target Python
> 3.12/3.13. Environment + lint: `uv` and `ruff`. HTTP: `httpx` (not `requests`). APIs: `FastAPI`
> + `Pydantic v2`. Data: `pandas`, but `Polars`/`DuckDB` when it needs to scale. Charts: `Plotly
> Express`. LLMs: call the provider SDK (or `LiteLLM`) directly — no agent framework for simple
> calls. Tests: `pytest` + `Hypothesis`. Retries: `tenacity`. Ask before adding heavy abstractions,
> and explain any library choice when a lighter or faster option exists.**

---

## Category → default pick (+ notable alternatives)

| Need | Default | Alternatives / when |
|---|---|---|
| Package/env manager | **uv** | pip+venv (legacy), PDM, poetry |
| Lint + format | **ruff** | (replaces flake8/isort/black) |
| Validation / schemas | **Pydantic v2** | msgspec (E, perf), attrs (no validation) |
| HTTP client | **httpx** | requests (sync scripts), aiohttp |
| API framework | **FastAPI** | Django (CRUD), Flask (minimal), Litestar (E) |
| ORM | **SQLAlchemy 2.0** | SQLModel (O, simpler CRUD), Peewee |
| Migrations | **Alembic** | — |
| Templating | **Jinja2** | Mako, Django templates |
| DataFrame | **pandas** | **Polars** (scale/speed), DuckDB (SQL) |
| Distributed data | **Ray / Dask** | Daft (E), Spark (Databricks), Ibis |
| Data validation | **pandera** | Great Expectations |
| Visualization | **Plotly Express** (interactive) | Seaborn (EDA), Matplotlib (PDF), pyecharts (exotic) |
| Graphs | **NetworkX** | Neo4j+driver (V, storage/GraphRAG), rustworkx (E) |
| Tabular ML | **scikit-learn + XGBoost/LightGBM** | CatBoost (categoricals), statsmodels (inference) |
| ML tracking | **MLflow** | — |
| Explainability | **SHAP** | — |
| Notebooks | **JupyterLab** | marimo (E, reactive/reproducible) |
| Data app / UI | **Streamlit** (demo) | NiceGUI (tools), Dash/Panel (viz), Reflex (E, real app) |
| Scraping (static) | **httpx + BeautifulSoup** | selectolax (volume), curl_cffi (E, TLS) |
| Scraping (JS) | **Playwright** | Scrapy (scale), Crawlee (E) |
| Scraping (for LLMs) | **Crawl4AI** (E) | Firecrawl (E), ScrapeGraphAI (X) |
| Call an LLM | **provider SDK** (anthropic/openai/google-genai) (V) | **LiteLLM** (multi-provider) |
| Structured LLM output | **Instructor** | Outlines (E), BAML (E) |
| LLM orchestration | **direct API first** | LangGraph (stateful), LlamaIndex (RAG) |
| Vector store | **Qdrant / pgvector** | Chroma (E, proto), FAISS, Weaviate/Milvus |
| Embeddings + rerank | **sentence-transformers / FlagEmbedding** | rank-bm25 (keyword half) |
| Doc parsing | **unstructured (E) / docling (E)** | pymupdf, pdfplumber |
| MCP server | **FastMCP** (E) | official `mcp` SDK, FastAPI-MCP (E) |
| PII redaction | **Presidio** | GLiNER, spaCy NER |
| Fine-tuning | **PEFT + Unsloth** (E) | Axolotl (E), TRL |
| LLM eval / tracing | **Ragas (E) + Langfuse (E)** | DeepEval, promptfoo, Phoenix |
| Testing | **pytest + Hypothesis** | Playwright (E2E), testcontainers (real deps) |
| Mock outbound HTTP | **respx** (httpx) / **responses** (requests) | moto (AWS), vcrpy |
| Observability | **OpenTelemetry + sentry-sdk** | prometheus-client, logfire (E) |
| Resilience / retries | **tenacity** | stamina (E), backoff, limits, slowapi |
| Report / doc gen | **python-docx / python-pptx / WeasyPrint** | reportlab, Quarto (E), MkDocs, Sphinx |
| CLI | **Typer** | Click, Rich (output) |
| Orchestration (jobs) | **Prefect / Dagster** | Airflow (incumbent) |
| AWS / GCP / Azure | **boto3 / google-cloud-\* / azure-\*** (V) | fsspec (cross-cloud storage) |
| Snowflake | **Snowpark** (V, in-warehouse) | connector (external), Cortex (AI) |
| Databricks | **pyspark + databricks-connect** (V) | databricks-sdk, deltalake |
| Salesforce (data) | **simple-salesforce** (sync) / **aiosalesforce** (E, async) | salesforce-api (E) |
| Salesforce (agents) | **agent-sdk** (V) | Agent API (REST) |
| Salesforce (DX) | **CumulusCI + `sf` CLI** (V) | — |

---

## Decision tree

- **Async or sync?** → real-time apps / chat / agents / high-concurrency → **async** (FastAPI + httpx + aiosalesforce). Linear ETL / scripts / cron → **sync** (requests or sync httpx, simple-salesforce) to skip asyncio boilerplate.
- **API?** → FastAPI · Django (CRUD) · Flask (minimal)
- **Data app?** → Streamlit (demo) · NiceGUI (tool) · Dash/Panel (viz) · Reflex (app)
- **Transform data?** → pandas → Polars (bigger) → DuckDB (SQL) → Ray/Daft/Spark (distributed). Don't reach for distributed on small data.
- **Data in a warehouse?** → Snowflake→Snowpark · Databricks→pyspark · small→just pandas/Polars
- **Tabular ML?** → sklearn + XGBoost/LightGBM/CatBoost (+ Optuna, SHAP, MLflow). Don't jump to deep learning for tabular.
- **Graphs?** → NetworkX (analysis) · Neo4j (storage/GraphRAG) · graphviz/PyVis (viz)
- **Charts?** → Seaborn (EDA) · Plotly (interactive) · Matplotlib (PDF) · pyecharts (exotic)
- **Call an LLM?** → provider SDK; + LiteLLM for multi-provider. No framework yet.
- **RAG?** → parse → chunk → embed → hybrid retrieve (vector + BM25) → rerank → generate → eval
- **Agents?** → provider tool-calling first → LangGraph / vendor Agent SDK when truly multi-step
- **Expose tools to AI?** → FastMCP server (OAuth 2.1 on HTTP transports)
- **Salesforce?** → simple/aiosalesforce (data) · agent-sdk (agents only) · CumulusCI+`sf` (DX)
- **Scrape?** → static→httpx+bs4 · JS→Playwright · for LLMs→Crawl4AI
- **Report/deck?** → python-docx/pptx · WeasyPrint+Jinja2 (PDF) · Quarto (technical docs)
- **Test?** → pytest+Hypothesis · Playwright (E2E). **Rule of thumb:** your own API → in-process client (`TestClient`) · outbound API → mock the client library (respx/responses/moto) · real infra → `testcontainers`.
- **Observability?** → OpenTelemetry + prometheus-client + sentry-sdk · tenacity/limits (resilience)

---

## Prompting AI tools (scaffolding / vibe coding)

**Name the stack up front** — steers the AI away from stale training-data defaults. Copy-paste:
- **"Scaffold a FastAPI service with SQLAlchemy 2.0, httpx, Pydantic, and pytest. Add pip-audit + detect-secrets to CI."**
- **"Build the dashboard in Streamlit; charts in Plotly Express."**
- **"Use `uv` for env/deps and `ruff` for lint+format."**
- **"Wrap the Salesforce and LLM calls in `tenacity` retries; they rate-limit."**

**Challenge a default** — when the AI reaches for something, ask why and offer the better fit:
- **"Why pandas here? This needs to scale — use Polars (or DuckDB for SQL)."**
- **"Why LangChain for a single provider call? Use the anthropic SDK directly."**
- **"Why `requests`? Prefer `httpx` — I want async + HTTP/2."**
- **"Why a raw dict from simple-salesforce? Switch to aiosalesforce for typed methods + async."**
- **"Why pickle for the weights? Use `safetensors`."**

**Guardrails to bake in** (regulated / enterprise):
- **"Redact PII with Presidio before logging or prompting."**
- **"Scope MCP tools with OAuth 2.1; keep a human approval step for writes."**
- **"Emit OpenTelemetry traces + Prometheus metrics."**
