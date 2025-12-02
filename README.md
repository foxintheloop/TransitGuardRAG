# TransitGuardRAG

**RAG-powered safety chatbot for Chicago transit.**

Backend API for the [TransitGuard](https://github.com/foxintheloop/TransitGuard) platform. Uses Pinecone vector search and Claude Haiku 3 to answer natural language questions about CTA safety incidents, station risk levels, and real-time crime data.

## Example Queries

- *"What are the stations near me?"*
- *"Total number of crimes today"*
- *"Safest line in the last 7 days"*
- *"Total number of traffic accidents today"*

## Architecture

```
Question → SentenceTransformer (all-MiniLM-L6-v2) → Embedding
                                                      ↓
                                              Pinecone Vector Search
                                                      ↓
                                              Top-K Context Chunks
                                                      ↓
                                              Claude Haiku 3 → Answer
```

---

## Features

- **/query endpoint:** Accepts a POST request with an embedding vector and question, returns a generated answer and sources.
- **/health endpoint:** Simple health check for the API.
- **Special queries:** Crime totals, traffic accidents, safest lines, nearby stations
- **Python client script:** Converts a question to an embedding and queries the API (local or Railway).
- **Dockerized & Railway-ready:** Easily build and run the API in a container or deploy to Railway.

---

## Project Structure

```
TransitGuardRAG/
│
├── main.py                   # FastAPI app for Pinecone + Claude querying
├── query_pinecone.py         # Python script to embed a question and query local API
├── railway_query_pinecone.py # Python script to query deployed Railway API
├── requirements.txt
├── Dockerfile
├── start.sh                  # Entrypoint for Railway
├── README.md
└── .env                      # Environment variables (not committed)
```

---

## Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/foxintheloop/TransitGuardRAG.git
cd TransitGuardRAG
```

### 2. Install Dependencies

```bash
pip install -r requirements.txt
```

### 3. Environment Variables

Create a `.env` file in the root directory:

```
PINECONE_API_KEY=your-pinecone-api-key
CLAUDE_API_KEY=your-anthropic-claude-api-key
```

### 4. Run Locally

```bash
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

---

## Docker Deployment

```bash
docker build -t transitguardrag .
docker run --env-file .env -p 8000:8000 transitguardrag
```

---

## Railway Deployment

1. Connect your repo to Railway
2. Set environment variables (`PINECONE_API_KEY`, `CLAUDE_API_KEY`) in the Railway dashboard
3. Deploy — Dockerfile and start.sh are pre-configured

---

## API Reference

### POST `/query`

Query the Pinecone index and generate an answer with Claude.

**Request:**

```json
{
  "embedding": [0.1, 0.2, 0.3, ...],
  "top_k": 5,
  "question": "What are the stations near me?"
}
```

**Response:**

```json
{
  "answer": "The stations near your current location are: ...",
  "sources": [ ... ]
}
```

### GET `/health`

Health check. Returns `{ "status": "healthy" }`.

---

## Python Client Example

```python
import requests
from sentence_transformers import SentenceTransformer

question = "What are the stations near me?"
model = SentenceTransformer('all-MiniLM-L6-v2')
embedding = model.encode(question).tolist()

response = requests.post(
    "https://your-railway-url.up.railway.app/query",
    json={"embedding": embedding, "top_k": 5, "question": question}
)
print(response.json())
```

---

## Stack

`FastAPI` `Pinecone` `Claude API` `SentenceTransformers` `Docker` `Railway`

---

## Related

- [TransitGuard](https://github.com/foxintheloop/TransitGuard) — Main project overview
- [transitguard-dashboard](https://github.com/foxintheloop/transitguard-dashboard) — Web dashboard
- [transitguard-app](https://github.com/foxintheloop/transitguard-app) — Mobile app

---

## License

MIT
