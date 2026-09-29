# Chat with Websites

A Streamlit chatbot that answers questions about a web page. You give it a URL, it reads the page, indexes the text in a Chroma vector store and then answers your questions from that content using LangChain and the OpenAI API.

## Features

- Paste a website URL in the sidebar and chat with the page's content
- Retrieval-augmented answers: only the chunks of the page most relevant to your question are sent to the model
- Follow-up questions work, because the chat history is used to rewrite each question into a search query before retrieval
- Entering a different URL loads the new page and starts a fresh conversation

## How it works

1. `WebBaseLoader` downloads the page and extracts its text with BeautifulSoup.
2. `RecursiveCharacterTextSplitter` splits the text into chunks.
3. The chunks are embedded with `OpenAIEmbeddings` and stored in an in-memory Chroma collection.
4. `create_history_aware_retriever` turns the latest message plus the chat history into a search query and fetches the relevant chunks.
5. `create_stuff_documents_chain` and `create_retrieval_chain` pass those chunks to `ChatOpenAI`, which writes the answer.

## Tech stack

- Python
- Streamlit for the chat UI
- LangChain (`langchain`, `langchain-community`, `langchain-openai`)
- OpenAI API for chat and embeddings
- ChromaDB as the vector store
- BeautifulSoup4 and python-dotenv

## Project structure

```
app.py            Streamlit app and the LangChain retrieval pipeline
requirements.txt  Python dependencies
.env.example      Template for the OpenAI API key
LICENSE
```

## Setup

You need Python 3.10 or 3.11 and an OpenAI API key. Some of the pinned packages are from early 2024 and have no prebuilt wheels for Python 3.12 or newer.

```bash
git clone https://github.com/Hamza-Tahirr/Conversational-AI-Chatbot-with-Langchain.git
cd Conversational-AI-Chatbot-with-Langchain

python -m venv venv
source venv/bin/activate        # on Windows: venv\Scripts\activate
pip install -r requirements.txt

cp .env.example .env            # on Windows: copy .env.example .env
```

Open `.env` and set your key:

```
OPENAI_API_KEY=your-openai-api-key
```

## Run

```bash
streamlit run app.py
```

Streamlit prints a local URL (usually http://localhost:8501). Open it, enter a website URL in the sidebar and start asking questions.

## Notes

- Only the page at the given URL is loaded. Links on the page are not followed.
- The vector store lives in memory, so the page is indexed again after every restart.
- Indexing and every question call the OpenAI API, so usage is billed to your key.

## License

MIT, see [LICENSE](LICENSE).
