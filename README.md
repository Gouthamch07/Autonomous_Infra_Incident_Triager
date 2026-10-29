# Autonomous-Infrastructure-Incident-Triager

An enterprise-grade, stateful AI orchestration system designed to automate the lifecycle of infrastructure incident triage, diagnostics, and self-healing. Built with Python, LangGraph, and FastAPI, this platform ingests raw server telemetry, isolates alphanumeric error signatures, references structured internal runbooks, and securely runs sandboxed diagnostic validation before halting for administrative approval.

---

## System Architecture

<img width="1541" height="838" alt="image" src="https://github.com/user-attachments/assets/afa20882-da5f-43fd-82ca-d5d27c77a018" />


## Technical Stack
* **Agentic Orchestration:** LangGraph (State Graph, Conditional Edges, MemorySaver Checkpointers)
* **API & Backend Layer:** Async Python, FastAPI, Pydantic v2
* **Data & Search Layer:** PostgreSQL (`pgvector`), Hybrid Search (BM25 + Dense Embeddings)
* **Isolation Framework:** Docker (`docker-py` SDK)
* **Message Broker / Cache:** Redis
* **Validation & Evaluation:** pytest, httpx

## Core Architecture Workflow
1. **Telemetry Ingestion:** A background streaming worker pipes simulated system crashes and infrastructure error logs into a local queue.
2. **LangGraph State Initialization:** The Supervisor node parses incoming metadata, provisions a shared state workspace (`TypedDict`), and triggers specialized worker agents.
3. **Log Audit & Metrics Extraction:** An asynchronous query worker targets the PostgreSQL metric layer, pulling historical system parameters (CPU spikes, memory leakage data) adjacent to the outage timestamp.
4. **Hybrid RAG Routing:** Because strict alphanumeric error signatures (e.g., `ERR_MEM_OOM_502`) confuse standard dense models, the retrieval layer executes a unified sparse-dense token search across markdown playbooks to guarantee runbook accuracy.
5. **Sandboxed Code Execution:** The agent synthesizes the metrics context to compile a short-lived recovery patch. The script is securely forwarded via the Docker SDK into an isolated container node to check for return-code termination safety.
6. **Human-in-the-Loop (HITL) Gateway:** Utilizing a compiled state checkpointer, the graph explicitly halts execution prior to production delivery. It persists the current state snapshot to disk and exposes an approval endpoint for system administrator validation.

## Testing & Verification
The execution paths and boundary constraints are fully mapped out using an automated testing harness:
```bash
# Run comprehensive integration and state machine routing tests
pytest tests/ -v
```
