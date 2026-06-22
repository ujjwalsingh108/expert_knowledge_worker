# Expert Knowledge Worker

An internal Q&A assistant for Insurellm that answers questions about company employees and products using Retrieval-Augmented Generation (RAG). The project progresses through multiple iterations, starting with a simple keyword-based approach and evolving into a production-ready semantic search pipeline.

## Project Overview

This project demonstrates the complete RAG pipeline across four progressive iterations:

1. **Day 1**: Simple keyword-based chat interface
2. **Day 2**: Advanced RAG with vector embeddings and visualization
3. **Day 3**: LangChain-based semantic retrieval with chat
4. **Day 4**: Production enhancements and deployment

## Day 1: Simple Chat Interface

**File**: `day1.ipynb`

A lightweight conversational AI prototype that uses keyword matching to retrieve context.

**Key Features**:
- Loads knowledge base from markdown files into memory
- Extracts relevant context using simple word matching
- Integrates with Gradio for an interactive web chat interface
- Uses Groq API via OpenAI-compatible endpoint

**Workflow**:
1. Load environment variables (API credentials)
2. Initialize OpenAI client with Groq base URL
3. Read employee and product markdown files into a Python dictionary
4. For each user query:
   - Extract matching keywords and find relevant documents
   - Build system prompt + context + chat history
   - Send to `openai.chat.completions.create(...)`
   - Display response in Gradio ChatInterface

**Limitations**: Keyword matching is brittle and context-unaware.

## Day 2: Vector Embeddings & Visualization

**File**: `day2.ipynb`

Builds a proper vector database with embeddings and provides visualization of the embedding space.

**Key Components**:
- **Part A - Chunking**: Splits documents into overlapping chunks (1000 characters with 200-char overlap)
- **Part B - Embeddings**: Converts chunks to 384-dimensional vectors using HuggingFace `all-MiniLM-L6-v2` model
- **Part C - Visualization**: Projects high-dimensional vectors to 2D/3D using t-SNE and displays with Plotly

**Technical Details**:
- Uses LangChain's `DirectoryLoader` and `RecursiveCharacterTextSplitter`
- Stores vectors in Chroma (local vector database)
- Computes token counts using HuggingFace tokenizers
- Creates interactive 2D and 3D scatter plots with document hover info

**Outputs**:
- Persistent vector database in `vector_db/` directory
- Interactive 3D visualization showing how documents cluster in embedding space
- Metadata tagging by document type (employees, products, contracts, company)

## Day 3: LangChain RAG Pipeline

**File**: `day3.ipynb`

Implements a production-ready RAG pipeline using LangChain with semantic search.

**Key Features**:
- Loads pre-built Chroma vector store from Day 2
- Uses LangChain's `Retriever` for semantic similarity search
- Integrates `ChatOpenAI` (connected to Groq) for response generation
- Configurable temperature control for output diversity
- Combines System Prompt + Retrieved Context + User Query

**Architecture**:
1. Initialize embeddings (HuggingFace `all-MiniLM-L6-v2`)
2. Connect to existing Chroma vectorstore
3. Create retriever: `vectorstore.as_retriever()`
4. Initialize LLM: `ChatOpenAI(model=groq_model, ...)`
5. For each query:
   - Retrieve top-k relevant chunks from vectorstore
   - Format with system prompt
   - Call LLM with retrieved context
   - Return response

**Advantages over Day 1**:
- Semantic search vs. keyword matching
- Reuses pre-computed embeddings (faster queries)
- LangChain abstraction for flexibility
- Configurable retrieval parameters (search type, k, similarity threshold)

## Project Structure

```
expert_knowledge_worker/
├── day1.ipynb                      # Simple keyword-based chat
├── day2.ipynb                      # Vector embeddings & visualization
├── day3.ipynb                      # LangChain RAG pipeline
├── day4.ipynb                      # Production enhancements
├── knowledge-base/
│   ├── company/                    # Company info (overview, culture, careers)
│   ├── employees/                  # Employee profiles (~35 markdown files)
│   ├── products/                   # Product descriptions (8 LLM products)
│   └── contracts/                  # Contract references
├── vector_db/                      # Chroma vector store (generated)
├── requirements.txt                # Python dependencies
├── README.md                       # This file
└── .env                           # Local environment variables (not committed)
```

## Setup

### Prerequisites

- Python 3.8+
- Groq API account with valid credentials

### Installation

1. Clone or pull the repository.

2. Create a virtual environment (optional but recommended):

```bash
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
```

3. Install dependencies:

```bash
pip install -r requirements.txt
```

4. Create a `.env` file in the project root:

```env
GROQ_API_KEY=your_groq_api_key
GROQ_MODEL=openai/gpt-oss-20b
GROQ_BASE_URL=https://api.groq.com/openai/v1
```

Replace with your actual Groq credentials.

## Running the Notebooks

### Day 1 - Quick Start

```bash
jupyter notebook day1.ipynb
```

Run all cells in order. The final cell launches a Gradio app (e.g., `http://127.0.0.1:7863`).

### Day 2 - Build Vector Database

```bash
jupyter notebook day2.ipynb
```

Generates the `vector_db/` directory with embeddings and creates 2D/3D visualizations.

### Day 3 - RAG Pipeline

```bash
jupyter notebook day3.ipynb
```

Loads the vector database and runs semantic search queries. Includes interactive Gradio chat.

### Day 4 - (Placeholder for production enhancements)

```bash
jupyter notebook day4.ipynb
```

## Technical Notes

### Context Retrieval Evolution

| Approach | Method | Accuracy | Speed | Scalability |
|----------|--------|----------|-------|-------------|
| **Day 1** | Keyword matching | Low | Fast | Poor |
| **Day 2** | Vector similarity | Medium-High | Medium | Good |
| **Day 3** | Semantic + LangChain | High | Medium | Excellent |

### Embeddings Model

- **Model**: HuggingFace `all-MiniLM-L6-v2`
- **Dimensions**: 384
- **Chunk Size**: 1000 characters
- **Overlap**: 200 characters
- **Advantage**: Fast, small, good for domain-specific tasks

### Vector Database

- **Tool**: Chroma (open-source, local)
- **Persistence**: SQLite + directory storage
- **Location**: `vector_db/`

## Troubleshooting

### Missing Dependencies

If you get import errors, reinstall:

```bash
pip install -r requirements.txt --upgrade
```

### API Key Issues

- Verify `.env` file exists and has correct keys
- Check that `GROQ_API_KEY` is valid and has quota
- Ensure `GROQ_BASE_URL` matches your Groq endpoint

### Gradio App Won't Launch

- Check that port 7863 (or assigned port) is free
- Try: `gr.ChatInterface(...).launch(server_name="localhost", server_port=7863)`

### Vector Database Not Found

- Run all cells in `day2.ipynb` first to generate `vector_db/`
- Check that `db_name = "vector_db"` matches the directory name

## Future Enhancements

- [ ] Hybrid search (keyword + semantic)
- [ ] Fine-tuned embeddings for domain
- [ ] Query expansion and reformulation
- [ ] Multi-hop reasoning over documents
- [ ] Fact verification against source
- [ ] Production API deployment (FastAPI)
- [ ] Logging and monitoring
- [ ] A/B testing different retrieval strategies