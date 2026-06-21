# Expert Knowledge Worker

An internal Q&A assistant for Insurellm that answers questions about company employees and products using a lightweight Retrieval-Augmented Generation (RAG) approach.

The implementation currently lives in `day1.ipynb` and uses:

- A file-based knowledge base from `knowledge-base/employees` and `knowledge-base/products`
- Keyword-based context retrieval from user questions
- Groq-hosted model access via the OpenAI Python client
- A Gradio chat interface for interactive use

## How It Works

1. Load environment variables from `.env`.
2. Initialize OpenAI client with Groq base URL.
3. Read markdown files into an in-memory `knowledge` dictionary.
4. Extract relevant snippets by matching words in the user question.
5. Build a system prompt + relevant context + chat history.
6. Send messages to `openai.chat.completions.create(...)`.
7. Serve chat UI through Gradio `ChatInterface`.

## Project Structure

- `day1.ipynb`: End-to-end prototype notebook.
- `knowledge-base/company`: Company overview and culture docs.
- `knowledge-base/employees`: Employee profile markdown files.
- `knowledge-base/products`: Product markdown files.
- `knowledge-base/contracts`: Contract reference documents.
- `requirements.txt`: Python dependencies.

## Setup

Install dependencies:

```bash
pip install -r requirements.txt
```

Create a `.env` file with:

```env
GROQ_API_KEY=your_api_key
GROQ_MODEL=your_model_name
GROQ_BASE_URL=your_groq_openai_compatible_base_url
```

Example model value used in the notebook:

```env
GROQ_MODEL=openai/gpt-oss-20b
```

## Run

Open and run cells in `day1.ipynb` in order.

The final cell launches the Gradio app locally (for example at `http://127.0.0.1:7863`).

## Notes

- Context retrieval is intentionally simple (word matching) and can be upgraded later to semantic retrieval.
- Gradio may pass extra message fields (like metadata) in history. The notebook includes `_normalize_history(...)` so only supported role/content pairs are sent to the model API.