# AI Knowledge Assistant - Lite

A smaller midterm-friendly RAG chatbot with conversational memory.

## Sources supported

- PDF
- PowerPoint (`.pptx`)
- Word (`.docx`)
- TXT / Markdown
- CSV
- Excel (`.xlsx`)
- JSON
- Google Sheets URL
- Public web page URL

It is intentionally a single-file Streamlit application so there is no separate ingestion script, no saved FAISS folder, and no database server to run.

## Architecture

```text
Files / Google Sheet / Web Page
              ↓
         Text Extraction
              ↓
            Chunking
              ↓
      FastEmbed Embeddings
              ↓
       Qdrant Vector DB
              ↓
          Top-K Retrieval
              ↓
      Groq + RAG Prompt
              ↑
    Full Conversation Memory
```

Qdrant supports a local in-memory mode for prototypes, and its current FastEmbed integration can generate embeddings directly when uploading/querying documents. The default `BAAI/bge-small-en-v1.5` model is listed by FastEmbed at about 0.067 GB, which keeps the embedding side lighter than a typical Transformers/Sentence-Transformers setup. 

## Setup on Windows

```powershell
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

Copy `.env.example` to `.env` and add your Groq key:

```env
GROQ_API_KEY=gsk_your_real_key_here
```

Then run:

```powershell
streamlit run app.py
```

Open the local Streamlit URL in your browser.

## How to use

1. Upload one or more files from the sidebar.
2. Optionally paste a Google Sheet URL or public webpage URL.
3. Click **Build Knowledge Base**.
4. Ask a question.
5. Ask a follow-up question such as `What about the second one?` to demonstrate memory.
6. Open **Retrieval details** to show the rewritten search query and retrieved sources.

## Requirement mapping

| Requirement | Lite implementation |
|---|---|
| Multiple knowledge sources | File uploads + Google Sheets + web page |
| Structured text | Each source is converted to text records |
| Embeddings | FastEmbed (`BAAI/bge-small-en-v1.5`) |
| Vector database | Qdrant local in-memory collection |
| Semantic search | Qdrant cosine similarity |
| RAG | Retrieved chunks inserted into the LLM prompt |
| Full chat memory | `InMemoryChatMessageHistory` |
| Follow-up questions | Previous chat + standalone-query rewriting |
| Testing | UI retrieval details + source display |

## Notes

This version focuses on common text-bearing sources. It does not perform OCR on images or speech-to-text for audio/video.

The Qdrant database is in memory, so the knowledge base is rebuilt when the Streamlit process restarts. That is intentional to keep the midterm version simple and small.
