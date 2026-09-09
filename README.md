# AI File Agent

A local AI document analysis platform that enables secure, privacy-first understanding of uploaded files using open-source LLMs, RAG, vector search, and autonomous agents.

>**Chat with your documents without sending them to the cloud.**

## Features

- **100% Local AI** – LLM inference runs through Ollama on your machine or server. No cloud dependency.
- **RAG-Based Document Understanding** – Retrieves relevant document sections via Qdrant vector search instead of loading entire files into context.
- **Agentic Workflow** – LangGraph agents autonomously decide when to search documents, perform calculations, retrieve context, and combine evidence.
- **Multiple File Types** – Supports PDF, DOCX, CSV, XLSX, TXT, and Markdown.
- **Evidence-Backed Answers** – Traces answers to specific documents, pages, and chunks to reduce hallucinations and ensure auditability.
- **Zero API Costs** – Local model inference eliminates per-token pricing.
- **Data Privacy** – Sensitive documents never leave your network.

## Technologies

* **Framework** : <a href="https://react.dev/">React</a>, <a href="https://fastapi.tiangolo.com/">FastAPI</a>, <a href="https://www.langchain.com/langgraph">LangGraph</a>
* **Language** : <a href="https://www.typescriptlang.org/">TypeScript</a>, <a href="https://www.python.org/">Python</a>
* **UI Components** : <a href="https://ui.shadcn.com/">shadcn/ui</a>
* **Styling** : <a href="https://tailwindcss.com/">Tailwind CSS</a>
* **AI/LLM** : <a href="https://ollama.com/">Ollama</a>, <a href="https://qwenlm.github.io/">Qwen</a>, <a href="https://www.llama.com/">Llama</a>, <a href="https://ai.google.dev/gemma">Gemma</a>
* **Vector Database** : <a href="https://qdrant.tech/">Qdrant</a>
* **Database** : <a href="https://www.postgresql.org/">PostgreSQL</a>
* **Infrastructure** : <a href="https://www.docker.com/">Docker</a> & <a href="https://docs.docker.com/compose/">Docker Compose</a>

## Installation
 
### Prerequisites
 
- Docker and Docker Compose
- Python 3.9+
- Node.js 16+
- Git
### Setup Steps
 
**1. Clone the repository and configure environment**
 
```bash
git clone https://github.com/ARUNAGIRINATHAN-K/ai-file-agent.git
cd ai-file-agent
cp .env.example .env
```
 
**2. Start infrastructure services**
 
```bash
docker compose up -d postgres qdrant ollama
```
 
**3. Pull required models**
 
```bash
docker exec ollama ollama pull llama3.1:8b
docker exec ollama ollama pull nomic-embed-text
```
 
**4. Run the backend (development)**
 
```bash
cd backend
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install -r requirements.txt
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```
 
**5. Run the frontend (development)**
 
In a new terminal:
 
```bash
cd frontend
npm install
npm run dev
```
 
## Quick Start
 
Once installation is complete, access the application:
 
- **Frontend:** http://localhost:3000
- **API Documentation:** http://localhost:8000/docs
- **Qdrant Dashboard:** http://localhost:6333/dashboard
### Uploading and Querying Documents
 
1. Navigate to http://localhost:3000
2. Upload a document (PDF, DOCX, CSV, XLSX, TXT, or Markdown)
3. Wait for the document to be processed and embedded
4. Ask questions about the document in the chat interface
5. View evidence-backed answers with source references
## Usage
 
The application supports:
 
- **Document Upload** – Process single or multiple documents
- **Natural Language Queries** – Ask questions in plain English
- **Agentic Responses** – Automatic search, calculation, and context retrieval
- **Source Attribution** – View document chunks that support answers


## Production Considerations

For self-hosted deployments, ensure:
- Persistent, performant storage for Qdrant
- Security and authentication layers
- Monitoring and observability
- Backup and disaster recovery procedures
- Model lifecycle management

For public SaaS deployments, additional considerations include GPU scheduling, horizontal inference scaling, and multi-user job queue management.

See the [Qdrant production guide](https://qdrant.tech/documentation/installation/) and [Ollama documentation](https://docs.ollama.com) for detailed configuration.

## Contributing

[Add contribution guidelines, development setup, and pull request process]

## License

[Add license type and link]

## References

- [LangChain ChatOllama Documentation](https://docs.langchain.com/oss/python/integrations/chat/ollama)
- [LangGraph Documentation](https://docs.langchain.com/oss/python/langgraph/overview)
- [Qdrant Quick Start](https://qdrant.tech/documentation/quick-start/)
- [Ollama API Reference](https://docs.ollama.com/api)

---

[![Star History Chart](https://api.star-history.com/svg?repos=ARUNAGIRINATHAN-K/ai-file-agent.git&type=Date)](https://star-history.com/ARUNAGIRINATHAN-K/ai-file-agent&Date)