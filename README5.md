# Infosys AI Interview Prep: RAG, LangChain, LangGraph, and Pinecone

This guide helps you explain your AI project clearly and confidently during an interview.

## 1. If the interviewer asks: "Which OpenAI model did you use?"

Use the model you actually integrated in your project. For example:

### Example answer

"I worked with OpenAI's GPT-4o model in my project. I integrated it using the OpenAI Python SDK.

I used it for:

1. Understanding user queries.
2. Generating context-aware responses.
3. Summarizing documents.
4. Building a question-answering system.
5. Integrating LLM responses into a RAG pipeline with embeddings and a vector database."

### Common follow-up questions

| Interview question | Good answer |
| --- | --- |
| Which model did you use? | GPT-4o (or the model you actually used) |
| How did you integrate it? | Using the OpenAI Python SDK |
| Which embedding model did you use? | text-embedding-3-small or text-embedding-3-large |
| How did you generate responses? | Chat Completions API or Responses API |
| How did you implement RAG? | Document chunking, embeddings, vector search, and LLM response generation |

### Simple Python example

```python
from openai import OpenAI

client = OpenAI(api_key="YOUR_API_KEY")

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {"role": "user", "content": "Explain Python decorators"}
    ]
)

print(response.choices[0].message.content)
```

## 2. Where did you use LangChain and LangGraph?

"I used LangChain for document processing and retrieval in the RAG pipeline. I used PDF loaders to extract text from documents, text splitters to create chunks, embeddings to convert text into vectors, and a vector database to retrieve relevant context.

I also used LangChain tools to connect external systems like database queries and API calls.

For workflow orchestration, I used LangGraph. I designed a graph-based workflow with states, nodes, tools, and conditional edges. Each node performed a specific task, and the workflow moved to the next step depending on the output of the previous node.

This helped us manage the underwriting and fraud detection workflow in a structured and scalable way."

This is a strong answer because it clearly explains both the technology and its purpose.

## 3. Interview-ready project explanation

You can explain your project like this:

"I implemented a Retrieval-Augmented Generation (RAG) pipeline for an AI-powered underwriting and fraud detection system.

For document processing, I used LangChain PDF loaders to extract text from underwriting documents. Then I used text splitters to divide the text into smaller chunks.

For embedding generation, I used the Hugging Face `all-MiniLM-L6-v2` model, which converts text chunks into 384-dimensional vectors.

I stored these vectors in Pinecone, a vector database designed for similarity search.

Whenever a user submits a query, I generate an embedding for that query using the same embedding model. I then perform a similarity search in Pinecone to retrieve the most relevant document chunks.

Finally, I pass the retrieved context together with the user query to the OpenAI LLM to generate a context-aware response.

I used LangGraph to orchestrate the complete workflow using states, nodes, tools, and conditional edges."

## 4. Architecture of the RAG implementation

1. PDF underwriting documents
   - Loan applications
   - Policies
   - Financial records

2. LangChain PDF Loader + Text Splitter
   - Extract text from PDFs
   - Split large documents into smaller chunks

3. Hugging Face embedding model
   - `all-MiniLM-L6-v2`
   - Generates 384-dimensional vectors

4. Pinecone vector database
   - Stores embeddings
   - Stores metadata and document IDs
   - Enables similarity search

5. User query
   - Query is converted into an embedding using the same model

6. Query embedding + similarity search
   - Retrieve the most relevant chunks from Pinecone

7. OpenAI LLM
   - Uses the retrieved chunks as context
   - Generates a final answer

8. Final response
   - Returns an accurate and context-aware response to the user

## 5. Important technical questions Infosys may ask

### Q1. Why did you use Pinecone?

Answer:

"I used Pinecone because it is a vector database designed for storing and searching high-dimensional embeddings. It supports fast similarity search, metadata filtering, and scalable retrieval."

### Q2. Why did you use all-MiniLM-L6-v2?

Answer:

"It is a lightweight sentence-transformer model that creates 384-dimensional embeddings. It gives a good balance between speed, efficiency, and semantic similarity performance."

### Q3. How did you store embeddings in Pinecone?

Answer:

"First, I generated embeddings for each document chunk. Then I stored the vectors in Pinecone along with unique IDs and relevant metadata such as document name and chunk position."

### Q4. How did you retrieve relevant documents?

Answer:

"When a user asks a question, I generate an embedding for the query using the same Hugging Face model. I then send the query vector to Pinecone and perform similarity search to retrieve the most relevant document chunks."

### Q5. What is cosine similarity?

Cosine similarity measures how similar two vectors are based on the angle between them.

\[
\text{Cosine Similarity} = \frac{A \cdot B}{\|A\|\|B\|}
\]

A higher value usually indicates greater similarity.

### Q6. What happens if the retrieved documents are irrelevant?

Answer:

"I would use a similarity threshold, metadata filtering, and retrieval validation to reduce irrelevant context before passing it to the LLM. If relevant information is not available, the system should say so rather than generate a misleading answer."

## 6. Key concepts to remember

| Technology | Responsibility |
| --- | --- |
| LangChain | PDF loading, chunking, and RAG integration |
| Hugging Face | Embedding generation |
| all-MiniLM-L6-v2 | Converts text into 384-dimensional vectors |
| Pinecone | Vector storage and similarity search |
| OpenAI | LLM response generation |
| LangGraph | Workflow orchestration |
| FastAPI | Backend API integration (if used) |

## 7. Tricky interview questions

These are common in technical interviews:

1. What is the difference between a vector database and a relational database?
2. What is the difference between semantic search and keyword search?
3. What is chunk size and chunk overlap?
4. Why should you use the same embedding model for documents and queries?
5. What is top-K retrieval?
6. How do you handle duplicate documents?
7. What is metadata filtering in Pinecone?
8. What happens when the embedding dimension does not match the Pinecone index dimension?
9. How do you reduce hallucinations in RAG?
10. How do you evaluate RAG performance?

## 8. Important distinction to remember

- Hugging Face creates embeddings.
- Pinecone stores and retrieves embeddings.
- LangChain connects the document-processing and retrieval workflow.
- OpenAI generates the final response from the retrieved context.
- LangGraph controls the execution flow.

This distinction helps you answer follow-up questions clearly and confidently.

## 9. Final interview tip

Practice explaining your real project using the flow below:

- Problem statement
- Data source and document processing
- Embeddings and vector storage
- Query and retrieval
- LLM response generation
- Workflow orchestration

## 10. Next step

I recommend practicing a mock technical interview focused on your actual project, especially around RAG, Pinecone, embeddings, LangChain, and LangGraph.

If you want, I can also help you prepare:

- a 2-minute interview answer,
- a 5-minute technical walkthrough,
- or a mock Infosys interview with follow-up questions.

"The best answers are simple, clear, and tied to the actual architecture of your project."