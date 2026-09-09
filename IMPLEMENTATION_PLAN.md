# Implementation Plan: Local AI File Agent

> Production-ready implementation plan for a privacy-first, local AI document analysis platform using Docker Compose, FastAPI, Ollama, Qdrant, LangGraph, and React/shadcn.

---

## 1. Project Overview

The **Local AI File Agent** is a full-stack application that allows users to upload documents, index them locally, and ask natural-language questions about their content.

The system uses open-source models through Ollama, stores document embeddings in Qdrant, orchestrates agent behavior with LangGraph, exposes a FastAPI backend, and provides a modern React/shadcn frontend.

The key differentiator is that the entire pipeline can run locally, making it suitable for privacy-sensitive documents.

---

## 2. Core Objectives

### Primary Goals

- [ ] Allow users to upload documents through a web UI.
- [ ] Extract text from PDF, DOCX, TXT, Markdown, CSV, and JSON files.
- [ ] Split documents into chunks suitable for retrieval.
- [ ] Generate embeddings locally using Ollama.
- [ ] Store embeddings and metadata in Qdrant.
- [ ] Build a LangGraph agent that decides when to retrieve document context.
- [ ] Answer user questions using retrieved context and a local LLM.
- [ ] Stream agent responses to the frontend.
- [ ] Provide document management capabilities.
- [ ] Containerize the entire stack with Docker Compose.
- [ ] Prepare the project for production deployment.

### Non-Goals for MVP

- [ ] Multi-tenant SaaS billing.
- [ ] Advanced user/role management.
- [ ] Fine-tuning custom models.
- [ ] OCR for scanned PDFs.
- [ ] Distributed Qdrant clustering.
- [ ] Multi-region deployment.

---

## 3. Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| Frontend | React, Next.js, TypeScript, Tailwind CSS, shadcn/ui | User interface |
| Backend | FastAPI, Python 3.12 | API server |
| Agent Orchestration | LangGraph | Agentic workflow |
| Local LLM Runtime | Ollama | Runs open-source models |
| Vector Database | Qdrant | Semantic document search |
| Metadata Database | PostgreSQL | Documents, users, conversations |
| Containerization | Docker, Docker Compose | Local and production deployment |
| Reverse Proxy | Nginx | Production routing |
| Testing | Pytest, Vitest, Playwright | Backend and frontend tests |

---

## 4. High-Level Architecture

```mermaid
graph TD
    User[User Browser] --> UI[React/shadcn Frontend]
    UI --> API[FastAPI Backend]
    API --> Agent[LangGraph Agent]
    Agent --> Qdrant[Qdrant Vector DB]
    Agent --> Ollama[Ollama LLM]
    API --> DocProcessor[Document Processor]
    DocProcessor --> Parser[PDF/DOCX/TXT Parser]
    DocProcessor --> Chunker[Text Chunker]
    Chunker --> Embedder[Ollama Embeddings]
    Embedder --> Qdrant
    API --> Postgres[PostgreSQL Metadata]
```

---

## 5. Planned User Experience

### Document Upload Flow

1. User opens the dashboard.
2. User uploads a PDF, DOCX, TXT, or Markdown file.
3. Backend validates the file.
4. Backend extracts text and creates chunks.
5. Backend generates embeddings locally.
6. Backend stores vectors in Qdrant and metadata in PostgreSQL.
7. Document appears in the document list as ready.

### Chat Flow

1. User selects one or more documents, or chats across all documents.
2. User asks a question.
3. Backend sends the question to the LangGraph agent.
4. Agent decides whether retrieval is required.
5. If required, agent searches Qdrant for relevant chunks.
6. Agent sends context and question to the local LLM.
7. Backend streams the response to the frontend.
8. UI displays the answer and source citations.

---

## 6. Repository Structure

```text
ai-analysis-agent/
├── docker-compose.yml
├── docker-compose.prod.yml
├── Makefile
├── .env.example
├── .gitignore
├── README.md
├── IMPLEMENTATION_PLAN.md
│
├── backend/
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── pyproject.toml
│   ├── alembic.ini
│   ├── alembic/
│   ├── app/
│   │   ├── main.py
│   │   ├── config.py
│   │   ├── api/
│   │   │   ├── deps.py
│   │   │   └── v1/
│   │   │       ├── router.py
│   │   │       └── endpoints/
│   │   │           ├── documents.py
│   │   │           ├── chat.py
│   │   │           └── health.py
│   │   ├── core/
│   │   │   ├── exceptions.py
│   │   │   ├── logging.py
│   │   │   └── security.py
│   │   ├── db/
│   │   │   ├── base.py
│   │   │   └── session.py
│   │   ├── models/
│   │   │   ├── document.py
│   │   │   └── conversation.py
│   │   ├── schemas/
│   │   │   ├── document.py
│   │   │   ├── chat.py
│   │   │   └── health.py
│   │   ├── services/
│   │   │   ├── document_processor.py
│   │   │   ├── embedding_service.py
│   │   │   ├── ollama_service.py
│   │   │   └── qdrant_service.py
│   │   └── agents/
│   │       ├── graph.py
│   │       ├── state.py
│   │       ├── tools.py
│   │       └── prompts.py
│   └── tests/
│       ├── conftest.py
│       ├── test_documents.py
│       ├── test_chat.py
│       └── test_agent.py
│
├── frontend/
│   ├── Dockerfile
│   ├── package.json
│   ├── next.config.js
│   ├── tailwind.config.ts
│   ├── components.json
│   └── src/
│       ├── app/
│       │   ├── layout.tsx
│       │   ├── page.tsx
│       │   ├── documents/
│       │   │   └── page.tsx
│       │   └── chat/
│       │       └── page.tsx
│       ├── components/
│       │   ├── ui/
│       │   ├── layout/
│       │   ├── chat/
│       │   └── documents/
│       ├── hooks/
│       │   ├── useChat.ts
│       │   └── useDocuments.ts
│       ├── lib/
│       │   ├── api.ts
│       │   ├── types.ts
│       │   └── utils.ts
│       └── styles/
│           └── globals.css
│
└── infrastructure/
    ├── nginx/
    │   ├── nginx.conf
    │   └── default.conf
    ├── postgres/
    │   └── init.sql
    └── scripts/
        ├── bootstrap.sh
        ├── pull-models.sh
        └── backup.sh
```

---

## 7. Milestones

This plan is organized into implementation milestones. Each milestone should map to a GitHub milestone or project board epic.

---

## Milestone 0: Repository Bootstrap

### Objective

Create the base repository, tooling, and development environment.

### Tasks

- [ ] Initialize Git repository.
- [ ] Create root folder structure.
- [ ] Add `.gitignore`.
- [ ] Add `.env.example`.
- [ ] Add `README.md`.
- [ ] Add `IMPLEMENTATION_PLAN.md`.
- [ ] Add `Makefile` for common commands.
- [ ] Configure Python linting with Ruff or Flake8.
- [ ] Configure frontend linting with ESLint.
- [ ] Configure formatting with Prettier and Black/Ruff Format.
- [ ] Add GitHub Actions CI workflow.

### Acceptance Criteria

- [ ] Repository can be cloned and bootstrapped with documented commands.
- [ ] Environment variable template exists.
- [ ] Linters run successfully.
- [ ] Basic CI pipeline runs on push and pull request.

---

## Milestone 1: Docker Compose Infrastructure

### Objective

Run all infrastructure services locally with Docker Compose.

### Services

- [ ] Ollama
- [ ] Qdrant
- [ ] PostgreSQL
- [ ] Backend
- [ ] Frontend
- [ ] Nginx, optional for production profile

### Tasks

- [ ] Create `docker-compose.yml`.
- [ ] Add named volumes for persistent storage.
- [ ] Add health checks for all services.
- [ ] Add internal Docker network.
- [ ] Add GPU support for Ollama if available.
- [ ] Add environment variable substitution.
- [ ] Create `docker-compose.prod.yml` override.
- [ ] Add script to pull required Ollama models.

### Required Models

```bash
ollama pull llama3.1:8b
ollama pull nomic-embed-text
```

### Acceptance Criteria

- [ ] `docker compose up -d` starts all services.
- [ ] Ollama responds at `http://localhost:11434`.
- [ ] Qdrant dashboard responds at `http://localhost:6333/dashboard`.
- [ ] PostgreSQL accepts connections from backend.
- [ ] Backend health endpoint responds at `http://localhost:8000/health`.
- [ ] Frontend responds at `http://localhost:3000`.

---

## Milestone 2: FastAPI Backend Skeleton

### Objective

Create a production-style FastAPI application structure.

### Tasks

- [ ] Create FastAPI app entrypoint.
- [ ] Add settings management with Pydantic Settings.
- [ ] Add structured logging.
- [ ] Add exception handlers.
- [ ] Add API versioning under `/api/v1`.
- [ ] Add health endpoint.
- [ ] Add CORS middleware.
- [ ] Add SQLAlchemy async session.
- [ ] Add Alembic migrations.
- [ ] Add dependency injection for database sessions.
- [ ] Add service layer pattern.

### Initial Endpoints

```text
GET  /health
GET  /api/v1/health
GET  /api/v1/documents
POST /api/v1/documents/upload
GET  /api/v1/documents/{id}
DELETE /api/v1/documents/{id}
POST /api/v1/chat/query
WS   /api/v1/chat/ws
```

### Acceptance Criteria

- [ ] FastAPI starts without errors.
- [ ] OpenAPI docs available at `/docs`.
- [ ] Health endpoint returns service status.
- [ ] Database migrations can be applied.
- [ ] Backend can connect to PostgreSQL.
- [ ] Backend can connect to Qdrant.
- [ ] Backend can connect to Ollama.

---

## Milestone 3: Document Upload and Processing

### Objective

Allow users to upload documents and convert them into searchable chunks.

### Supported File Types

- [ ] `.pdf`
- [ ] `.docx`
- [ ] `.txt`
- [ ] `.md`
- [ ] `.csv`
- [ ] `.json`

### Tasks

- [ ] Create document upload endpoint.
- [ ] Validate MIME type and extension.
- [ ] Enforce maximum file size.
- [ ] Store uploaded files in persistent volume.
- [ ] Extract text based on file type.
- [ ] Normalize whitespace and metadata.
- [ ] Split text into chunks.
- [ ] Store document metadata in PostgreSQL.
- [ ] Mark document processing status.
- [ ] Return processing errors cleanly.

### Document Processing Statuses

```text
uploaded
processing
ready
failed
```

### Chunking Strategy

- [ ] Target chunk size: 800 to 1200 tokens.
- [ ] Chunk overlap: 100 to 200 tokens.
- [ ] Preserve headings where possible.
- [ ] Store chunk index and document reference.

### Acceptance Criteria

- [ ] User can upload a document via API.
- [ ] File is saved to persistent storage.
- [ ] Text is extracted successfully.
- [ ] Document chunks are created.
- [ ] Document status becomes `ready`.
- [ ] Unsupported files return HTTP 400.
- [ ] Empty or unreadable files return a clear error.

---

## Milestone 4: Embedding and Qdrant Indexing

### Objective

Convert document chunks into vector embeddings and store them in Qdrant.

### Tasks

- [ ] Create Qdrant collection if it does not exist.
- [ ] Define vector size based on embedding model.
- [ ] Generate embeddings using Ollama.
- [ ] Batch embedding requests for performance.
- [ ] Upsert vectors into Qdrant.
- [ ] Store document and chunk metadata in payload.
- [ ] Add deletion cascade when a document is removed.
- [ ] Add reindexing endpoint or admin command.

### Qdrant Collection

```text
Collection name: documents
Vector size: depends on embedding model
Distance metric: Cosine
```

### Payload Schema

```json
{
  "document_id": "uuid",
  "filename": "example.pdf",
  "chunk_index": 12,
  "text": "Chunk text content",
  "page_number": 4,
  "created_at": "2026-01-01T00:00:00Z"
}
```

### Acceptance Criteria

- [ ] Uploaded documents are embedded locally.
- [ ] Vectors are stored in Qdrant.
- [ ] Payload includes enough metadata for citations.
- [ ] Semantic search returns relevant chunks.
- [ ] Deleting a document removes its vectors.
- [ ] Embedding failures are logged and surfaced.

---

## Milestone 5: LangGraph Agent

### Objective

Implement an agent workflow that can reason about whether retrieval is needed and answer using document context.

### Agent State

```python
class AgentState(TypedDict):
    messages: list
    conversation_id: str
    document_ids: list[str] | None
    needs_search: bool
    search_query: str
    retrieved_chunks: list
    context: str
    final_answer: str
```

### Graph Nodes

- [ ] `analyze_query`
- [ ] `retrieve_context`
- [ ] `generate_response`
- [ ] `handle_error`

### Conditional Logic

```text
analyze_query
  ├── needs_search == true  -> retrieve_context
  └── needs_search == false -> generate_response

retrieve_context -> generate_response
generate_response -> END
```

### Tasks

- [ ] Define agent state.
- [ ] Create query analysis node.
- [ ] Create retrieval node.
- [ ] Create response generation node.
- [ ] Add citations/source metadata to response.
- [ ] Add guardrails against hallucination.
- [ ] Add token-budget handling.
- [ ] Add fallback when no relevant context is found.
- [ ] Add tracing/logging for agent steps.
- [ ] Add unit tests for graph routing.

### System Prompt Requirements

The agent must:

- [ ] Use retrieved context as the primary source.
- [ ] Avoid inventing unsupported facts.
- [ ] Say when the document does not contain enough information.
- [ ] Cite filenames or chunk references when possible.
- [ ] Distinguish inference from explicit statements.

### Acceptance Criteria

- [ ] Agent can answer document-specific questions.
- [ ] Agent retrieves relevant chunks from Qdrant.
- [ ] Agent does not require external cloud APIs.
- [ ] Agent returns source metadata.
- [ ] Agent handles missing context gracefully.
- [ ] Agent graph can be tested independently.

---

## Milestone 6: Chat API and Streaming

### Objective

Expose chat endpoints for synchronous and streaming interactions.

### Endpoints

```text
POST /api/v1/chat/query
WS   /api/v1/chat/ws
GET  /api/v1/conversations
GET  /api/v1/conversations/{id}/messages
```

### Tasks

- [ ] Create chat request schema.
- [ ] Create chat response schema.
- [ ] Implement synchronous query endpoint.
- [ ] Implement WebSocket endpoint.
- [ ] Stream agent node updates.
- [ ] Stream LLM tokens where possible.
- [ ] Store conversations in PostgreSQL.
- [ ] Store messages in PostgreSQL.
- [ ] Add conversation-scoped document filters.
- [ ] Add request validation and rate limiting.

### Chat Request Example

```json
{
  "message": "What is the main argument of this paper?",
  "conversation_id": "optional-uuid",
  "document_ids": ["optional-document-uuid"]
}
```

### Chat Response Example

```json
{
  "conversation_id": "uuid",
  "answer": "The main argument is...",
  "sources": [
    {
      "document_id": "uuid",
      "filename": "paper.pdf",
      "chunk_index": 4,
      "score": 0.83
    }
  ]
}
```

### Acceptance Criteria

- [ ] User can send a question and receive an answer.
- [ ] User can continue a conversation.
- [ ] WebSocket streams partial updates.
- [ ] API validates malformed requests.
- [ ] Conversations persist across restarts.
- [ ] Chat can be restricted to selected documents.

---

## Milestone 7: React/shadcn Frontend

### Objective

Build a clean web interface for document management and chat.

### Pages

- [ ] Dashboard
- [ ] Documents
- [ ] Chat
- [ ] Settings

### Components

- [ ] Sidebar
- [ ] Header
- [ ] Document upload button
- [ ] Document table/list
- [ ] Document status badge
- [ ] Chat message list
- [ ] Chat input
- [ ] Streaming response indicator
- [ ] Source citation panel
- [ ] Error banner
- [ ] Empty states

### Tasks

- [ ] Initialize Next.js app.
- [ ] Install Tailwind CSS.
- [ ] Install shadcn/ui.
- [ ] Create API client.
- [ ] Create TypeScript types.
- [ ] Create document upload flow.
- [ ] Create document list view.
- [ ] Create chat view.
- [ ] Add WebSocket support.
- [ ] Add optimistic UI updates.
- [ ] Add loading skeletons.
- [ ] Add error handling.
- [ ] Add responsive layout.

### Acceptance Criteria

- [ ] User can upload a document from the UI.
- [ ] User can see document processing status.
- [ ] User can delete a document.
- [ ] User can ask questions in chat.
- [ ] User sees streamed answers.
- [ ] User sees source citations.
- [ ] UI works on desktop and mobile.
- [ ] UI handles API errors gracefully.

---

## Milestone 8: Production Hardening

### Objective

Prepare the application for reliable production deployment.

### Tasks

- [ ] Add JWT or session authentication.
- [ ] Add API rate limiting.
- [ ] Add request ID logging.
- [ ] Add structured JSON logs.
- [ ] Add Prometheus metrics.
- [ ] Add Grafana dashboard, optional.
- [ ] Add Nginx reverse proxy.
- [ ] Add HTTPS/TLS configuration.
- [ ] Add secrets management.
- [ ] Add database backups.
- [ ] Add Qdrant backup strategy.
- [ ] Add file upload malware scanning, optional.
- [ ] Add resource limits in Docker Compose.
- [ ] Add backend replicas behind load balancer.
- [ ] Add graceful shutdown handling.
- [ ] Add health checks for dependencies.

### Security Checklist

- [ ] No secrets committed to Git.
- [ ] CORS restricted to known origins.
- [ ] File uploads validated.
- [ ] Uploaded files stored outside web root.
- [ ] API authentication enabled.
- [ ] Database credentials stored securely.
- [ ] Container images pinned to specific versions.
- [ ] Dependencies scanned for vulnerabilities.

### Acceptance Criteria

- [ ] Application survives service restarts.
- [ ] Data persists in volumes.
- [ ] Logs are structured and searchable.
- [ ] Health checks report dependency failures.
- [ ] Production compose file works separately from development compose.
- [ ] Backup and restore process is documented.

---

## Milestone 9: Testing and Documentation

### Objective

Ensure the project is maintainable, testable, and easy to onboard.

### Backend Tests

- [ ] Unit tests for document processor.
- [ ] Unit tests for chunking logic.
- [ ] Unit tests for embedding service.
- [ ] Unit tests for Qdrant service.
- [ ] Integration tests for FastAPI endpoints.
- [ ] LangGraph routing tests.
- [ ] Error handling tests.

### Frontend Tests

- [ ] Component tests with Vitest and React Testing Library.
- [ ] Hook tests.
- [ ] API client tests.
- [ ] End-to-end tests with Playwright.

### Documentation

- [ ] README quickstart.
- [ ] Environment variable reference.
- [ ] Docker deployment guide.
- [ ] GPU setup guide.
- [ ] Troubleshooting guide.
- [ ] API documentation.
- [ ] Architecture decision records, optional.

### Acceptance Criteria

- [ ] CI runs backend and frontend tests.
- [ ] Coverage is tracked.
- [ ] All critical paths have tests.
- [ ] New developer can run project from README.
- [ ] Production deployment steps are documented.

---

## 8. Environment Variables

```env
# Application
APP_NAME=ai-analysis-agent
ENVIRONMENT=development
LOG_LEVEL=info
SECRET_KEY=change-me

# PostgreSQL
POSTGRES_USER=aiagent
POSTGRES_PASSWORD=change-me
POSTGRES_DB=aiagent_db
DATABASE_URL=postgresql+asyncpg://aiagent:change-me@postgres:5432/aiagent_db

# Ollama
OLLAMA_BASE_URL=http://ollama:11434
LLM_MODEL=llama3.1:8b
EMBEDDING_MODEL=nomic-embed-text

# Qdrant
QDRANT_URL=http://qdrant:6333
QDRANT_COLLECTION_NAME=documents
QDRANT_API_KEY=

# File Uploads
UPLOAD_DIR=/app/uploads
MAX_UPLOAD_SIZE_MB=50
ALLOWED_FILE_EXTENSIONS=.pdf,.docx,.txt,.md,.csv,.json

# Frontend
NEXT_PUBLIC_API_URL=http://localhost:8000/api/v1
NEXT_PUBLIC_WS_URL=ws://localhost:8000/api/v1/chat/ws

# CORS
CORS_ORIGINS=http://localhost:3000
```

---

## 9. API Specification

### Health

```text
GET /health
GET /api/v1/health
```

### Documents

```text
POST   /api/v1/documents/upload
GET    /api/v1/documents
GET    /api/v1/documents/{document_id}
DELETE /api/v1/documents/{document_id}
POST   /api/v1/documents/{document_id}/reindex
```

### Chat

```text
POST /api/v1/chat/query
WS   /api/v1/chat/ws
```

### Conversations

```text
GET /api/v1/conversations
GET /api/v1/conversations/{conversation_id}
GET /api/v1/conversations/{conversation_id}/messages
```

---

## 10. Database Schema

### documents

| Column | Type | Notes |
|---|---|---|
| id | UUID | Primary key |
| filename | TEXT | Original filename |
| file_path | TEXT | Stored file location |
| file_size | BIGINT | Size in bytes |
| mime_type | TEXT | MIME type |
| status | TEXT | uploaded, processing, ready, failed |
| num_chunks | INTEGER | Number of chunks |
| error_message | TEXT | Nullable |
| created_at | TIMESTAMP | Creation time |
| updated_at | TIMESTAMP | Last update |

### conversations

| Column | Type | Notes |
|---|---|---|
| id | UUID | Primary key |
| title | TEXT | Optional conversation title |
| created_at | TIMESTAMP | Creation time |
| updated_at | TIMESTAMP | Last update |

### messages

| Column | Type | Notes |
|---|---|---|
| id | UUID | Primary key |
| conversation_id | UUID | Foreign key |
| role | TEXT | user, assistant, system |
| content | TEXT | Message content |
| sources | JSONB | Retrieved source metadata |
| created_at | TIMESTAMP | Creation time |

---

## 11. Qdrant Data Model

```json
{
  "id": "document_id:chunk_index",
  "vector": [0.012, -0.034, 0.056],
  "payload": {
    "document_id": "uuid",
    "filename": "example.pdf",
    "chunk_index": 12,
    "text": "The actual chunk text.",
    "page_number": 4,
    "section": "Introduction",
    "created_at": "2026-01-01T00:00:00Z"
  }
}
```

---

## 12. LangGraph Design

```mermaid
graph TD
    A[User Query] --> B[Analyze Query]
    B -->|Needs Search| C[Retrieve Context]
    B -->|No Search Needed| D[Generate Response]
    C --> D
    D --> E[Return Answer + Sources]
```

### Node Responsibilities

#### `analyze_query`

- Parses the user question.
- Determines whether document retrieval is required.
- Produces a search query.

#### `retrieve_context`

- Embeds the search query.
- Searches Qdrant.
- Applies document filters if provided.
- Returns top relevant chunks.

#### `generate_response`

- Builds a grounded prompt.
- Sends prompt to local LLM.
- Returns answer and citations.

#### `handle_error`

- Handles retrieval failures.
- Handles LLM timeouts.
- Returns a user-friendly fallback message.

---

## 13. Suggested Makefile

```makefile
.PHONY: help up down logs models backend frontend test

help:
	@echo "Available commands:"
	@echo "  make up       - Start all services"
	@echo "  make down     - Stop all services"
	@echo "  make logs     - Tail logs"
	@echo "  make models   - Pull required Ollama models"
	@echo "  make backend  - Run backend locally"
	@echo "  make frontend - Run frontend locally"
	@echo "  make test     - Run tests"

up:
	docker compose up -d

down:
	docker compose down

logs:
	docker compose logs -f

models:
	docker exec ollama ollama pull llama3.1:8b || true
	docker exec ollama ollama pull nomic-embed-text || true

backend:
	cd backend && uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

frontend:
	cd frontend && npm run dev

test:
	cd backend && pytest
	cd frontend && npm run test
```

---

## 14. Local Development Commands

### Start Infrastructure

```bash
docker compose up -d postgres qdrant ollama
```

### Pull Models

```bash
docker exec -it ollama ollama pull llama3.1:8b
docker exec -it ollama ollama pull nomic-embed-text
```

### Run Backend Locally

```bash
cd backend
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

### Run Frontend Locally

```bash
cd frontend
npm install
npm run dev
```

### Run Full Stack

```bash
docker compose up -d --build
```

---

## 15. Test Plan

### Functional Tests

- [ ] Upload a valid PDF.
- [ ] Upload a valid DOCX.
- [ ] Upload a valid TXT file.
- [ ] Reject unsupported file type.
- [ ] Reject file exceeding size limit.
- [ ] Document appears in document list.
- [ ] Document status changes from processing to ready.
- [ ] Ask a question about an uploaded document.
- [ ] Receive an answer grounded in the document.
- [ ] Receive sources with the answer.
- [ ] Delete document and confirm vectors are removed.

### Agent Tests

- [ ] Query requiring retrieval triggers retrieval node.
- [ ] Query not requiring retrieval skips retrieval node.
- [ ] Empty Qdrant result returns no-context fallback.
- [ ] Large context is truncated safely.
- [ ] Agent does not invent unsupported facts.
- [ ] Agent handles LLM timeout gracefully.

### Non-Functional Tests

- [ ] API responds within acceptable latency.
- [ ] WebSocket streams without dropping messages.
- [ ] Docker services restart cleanly.
- [ ] Volumes preserve data after restart.
- [ ] Backend handles concurrent uploads.
- [ ] Qdrant handles repeated reindexing.

---

## 16. Definition of Done

The project is considered MVP-complete when:

- [ ] Users can upload documents through the frontend.
- [ ] Documents are processed locally.
- [ ] Document embeddings are stored in Qdrant.
- [ ] Users can chat with documents.
- [ ] Answers are generated by a local Ollama model.
- [ ] LangGraph orchestrates retrieval decisions.
- [ ] Responses include source citations.
- [ ] No external paid AI API is required.
- [ ] The full stack runs with Docker Compose.
- [ ] Health checks pass.
- [ ] Backend and frontend tests pass.
- [ ] README explains setup and usage.
- [ ] Production deployment path is documented.

---

## 17. Future Enhancements

### Short-Term

- [ ] OCR support for scanned PDFs.
- [ ] Multi-document chat filters.
- [ ] Conversation renaming.
- [ ] Export chat transcripts.
- [ ] Dark mode.
- [ ] Better citation preview.
- [ ] Document preview pane.

### Medium-Term

- [ ] User authentication and authorization.
- [ ] Workspace/project separation.
- [ ] Hybrid search with keyword + vector retrieval.
- [ ] Reranking model for improved retrieval.
- [ ] Background task queue with Celery or ARQ.
- [ ] Admin dashboard.
- [ ] Model selection UI.

### Long-Term

- [ ] Multi-node Qdrant deployment.
- [ ] Kubernetes Helm chart.
- [ ] Fine-tuned domain-specific models.
- [ ] Local speech-to-text input.
- [ ] Local text-to-speech output.
- [ ] Plugin system for external tools.
- [ ] Enterprise SSO integration.

---

## 18. GitHub Project Board Suggestion

Create these labels:

```text
infrastructure
backend
frontend
agent
rag
testing
documentation
security
bug
enhancement
```

Create these milestones:

```text
M0: Repository Bootstrap
M1: Docker Compose Infrastructure
M2: FastAPI Backend Skeleton
M3: Document Upload and Processing
M4: Embedding and Qdrant Indexing
M5: LangGraph Agent
M6: Chat API and Streaming
M7: React/shadcn Frontend
M8: Production Hardening
M9: Testing and Documentation
```

Create issues from each task checkbox in this document.

---

## 19. Immediate Next Steps

1. [ ] Create the GitHub repository.
2. [ ] Add this file as `IMPLEMENTATION_PLAN.md`.
3. [ ] Create the folder structure.
4. [ ] Add Docker Compose infrastructure