# FinSight AI — Financial Report RAG Assistant

A complete local web application built with **Python, FastAPI, FAISS, LangChain text splitters, Sentence Transformers**, and a responsive HTML/CSS/JavaScript frontend.

## Features
- Upload multiple PDF, DOCX, CSV, TXT, or Markdown financial documents.
- Split document text into overlapping chunks using LangChain.
- Embed chunks with `all-MiniLM-L6-v2` and retrieve via FAISS cosine similarity.
- Ask questions and inspect retrieved excerpts with filename, page (PDF), and similarity score.
- Optional OpenAI or local Ollama LLM for grounded, cited answers.
- Works without any LLM API key in **extractive evidence mode**.
- Demo financial report included.

## Folder structure
```
financial_rag_assistant/
├── backend/
│   ├── __init__.py
│   └── main.py
├── frontend/
│   ├── index.html
│   ├── style.css
│   └── app.js
├── sample_data/
│   └── example_annual_report.txt
├── requirements.txt
├── .env.example
├── start_windows.bat
└── README.md
```

## Setup (Windows PowerShell)
1. Install **Python 3.11** (recommended), checking **Add Python to PATH**.
2. Extract the ZIP, then open PowerShell in the `financial_rag_assistant` folder.
3. Run:
   ```powershell
   py -3.11 -m venv .venv
   .\.venv\Scripts\Activate.ps1
   python -m pip install --upgrade pip
   pip install -r requirements.txt
   uvicorn backend.main:app --reload --host 127.0.0.1 --port 8000
   ```
4. Visit **http://127.0.0.1:8000**. Upload `sample_data/example_annual_report.txt` to test.
5. Alternatively, double-click `start_windows.bat` after installing Python; it creates a venv and installs dependencies on first run.

> If PowerShell prevents activation, use `.venv\Scripts\python.exe` directly or run `Set-ExecutionPolicy -Scope Process Bypass`.

## LLM options (optional)
Without configuration, the app shows relevant document excerpts instead of claiming to synthesize an LLM answer.

**OpenAI:** In the terminal before starting:
```powershell
$env:OPENAI_API_KEY="your_api_key_here"
$env:OPENAI_MODEL="gpt-4o-mini"
uvicorn backend.main:app --reload
```
Do not commit or share API keys. OpenAI usage may incur charges and sends selected excerpts to OpenAI.

**Local Ollama:** Install Ollama, run `ollama pull llama3.2`, then:
```powershell
$env:OLLAMA_MODEL="llama3.2"
uvicorn backend.main:app --reload
```
If both are configured, OpenAI takes priority.

## API
- `GET /api/health` — status
- `GET /api/documents` — indexed documents
- `POST /api/upload` — multipart `files` array
- `POST /api/ask` — JSON `{ "question": "What was revenue in 2025?", "top_k": 5 }`
- `DELETE /api/documents` — clear current session
- `GET /docs` — Swagger UI

## Important limitations
- Embedding model downloads on first use (internet needed for initial download).
- PDF extraction is text-based; scanned/image-only PDFs require OCR (not included).
- Index is **in memory**, shared by everyone using this server and lost on restart; no accounts or access controls. Run locally with trusted files only. Do not expose to the public internet or upload sensitive corporate financial information.
- Financial tables can be extracted imperfectly; check cited passages and original statements.
- This is an educational prototype, not investment advice. It does not independently verify financial facts.
- Install dependencies in a virtual environment. Some packages may require wheels compatible with your Python version.
