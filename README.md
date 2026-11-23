---
title: JanSahayak AI
emoji: 🏛️
colorFrom: yellow
colorTo: green
sdk: docker
app_port: 7860
pinned: false
---

# JanSahayak (जनसहायक) — Government Document Intelligence & Citizen Assistant

JanSahayak is an AI-powered document intelligence and citizen assistance platform designed to make government schemes, circulars, and policies accessible, transparent, and understandable for citizens in both **Hindi** and **English**.

Powered by a **Hybrid Search (Dense Semantic + Sparse BM25)** engine and **Grounded RAG (Retrieval-Augmented Generation)** using Google Gemini, JanSahayak guarantees answers directly backed by genuine government documents with source citations.

---

## Key Features

- **Multilingual Support (Hindi & English):** Ask questions in Hindi or English and receive answers in the same language.
- **Strictly Grounded RAG:** Uses official gazettes, schemes, and circulars; avoids hallucinating benefits, eligibility, or amounts.
- **Hybrid Retrieval Engine:** Combines dense multilingual semantic vectors (`paraphrase-multilingual-MiniLM-L12-v2`) with sparse keyword matching (`BM25Okapi`) for maximum precision on acronyms and specific criteria.
- **Pre-Indexed Knowledge Base:** Includes persistent vector storage for Central, Madhya Pradesh, and Rajasthan government documents.
- **Document Classification & Extraction:** Categorizes government records into Schemes, Policies, and Circulars and extracts financial thresholds.
- **Citizen-Centric Web Interface:** Designed with Indian public service accessibility standards, bilingual typography, font-scaling, and one-click citation checks.

---

## Architecture Overview

```
[Raw Govt Circulars & Schemes (PDFs)]
                  │
                  ▼
   [Layer 1: OCR & Ingestion]
   • pdfplumber & Tesseract OCR (Hindi + English)
                  │
                  ▼
   [Layer 2: Entity & Category Extraction]
   • spaCy NER + Indian Currency/Percent Regex
   • TF-IDF + Logistic Regression Scheme Classifier
                  │
                  ▼
   [Layer 3: Embeddings & Vector Store]
   • Multilingual MiniLM Embeddings
   • ChromaDB Persistent Storage
                  │
                  ▼
   [Layer 4: Hybrid Search & Grounded Generation]
   • 50% Semantic (Cosine) + 50% Lexical (BM25)
   • Google Gemini Flash-Lite for Grounded Answers
                  │
                  ▼
   [Layer 5: API & UI Presentation]
   • FastAPI REST Backend
   • Responsive Citizen Portal Frontend
```

---

## Project Structure

```
janshayak/
├── backend/
│   ├── __init__.py
│   └── main.py                  # FastAPI REST API server
├── data/
│   └── vector_store/            # ChromaDB persistent vector database
├── frontend/
│   └── index.html               # Web portal (Public Sans + Noto Sans Devanagari)
├── scripts/
│   ├── ingest.py                # PDF extraction & multilingual OCR
│   ├── ner_extract.py           # Entity extraction (money, orgs, dates)
│   ├── auto_label.py            # Rule-based auto-labeling
│   ├── train_classifier.py      # Logistic Regression scheme classifier
│   ├── build_vector_store.py    # Chunking & vector store creation
│   ├── hybrid_search.py         # Semantic + BM25 search CLI
│   ├── rag.py                   # Command-line RAG interface
│   ├── summarize.py             # Document summarizer with Gemini
│   ├── test_search.py           # Retrieval verification script
│   └── check_data_quality.py    # Dataset quality audit
├── Dockerfile                   # Production Docker container definition
├── Procfile                     # Deployment process config (Render/Railway/Heroku)
├── requirements.txt             # Core production dependencies
├── requirements_full.txt        # Full dependencies including OCR & training tools
└── .env.example                 # Example environment variables
```

---

## Quickstart Guide

### 1. Clone the Repository
```bash
git clone https://github.com/ujjawal07msd/janshayak.git
cd janshayak
```

### 2. Set Up Virtual Environment & Dependencies
```bash
python -m venv venv

# On Windows:
venv\Scripts\activate

# On Linux/macOS:
source venv/bin/activate

pip install -r requirements.txt
```

### 3. Configure Environment Variables
Create a `.env` file in the root directory:
```env
GEMINI_API_KEY="your_google_gemini_api_key_here"
PORT=8000
```

### 4. Run the Backend API
```bash
uvicorn backend.main:app --reload --port 8000
```
The API documentation will be available at [http://localhost:8000/docs](http://localhost:8000/docs).

### 5. Access the Web Interface
Open `frontend/index.html` in your web browser, or navigate to [http://localhost:8000/frontend/](http://localhost:8000/frontend/) while the server is running.

---

## API Reference

### Health Check
- **Endpoint:** `GET /`
- **Response:**
  ```json
  {
    "status": "JanSahayak API chal raha hai",
    "total_chunks": 435
  }
  ```

### List Documents
- **Endpoint:** `GET /documents`
- **Response:** Returns list of indexed government documents with their jurisdiction.

### Ask a Question (RAG)
- **Endpoint:** `POST /ask`
- **Payload:**
  ```json
  {
    "question": "What is the scholarship amount for girl students in AICTE Pragati scheme?"
  }
  ```
- **Response:**
  ```json
  {
    "answer": "Under the AICTE Pragati Scholarship Scheme, eligible girl students receive...",
    "sources": [
      {
        "filename": "AICTE_3040_G.pdf",
        "jurisdiction": "central"
      }
    ]
  }
  ```

---

## Deployment

### Docker
```bash
# Build the image
docker build -t janshayak .

# Run the container
docker run -p 8000:8000 -e GEMINI_API_KEY="your_api_key" janshayak
```

### Cloud Platforms (Render / Railway)
1. Link your GitHub repository `ujjawal07msd/janshayak`.
2. Set Build Command: `pip install -r requirements.txt`.
3. Set Start Command: `uvicorn backend.main:app --host 0.0.0.0 --port $PORT`.
4. Add Environment Variable: `GEMINI_API_KEY`.

---

## License
MIT License. Open for public and educational use.

