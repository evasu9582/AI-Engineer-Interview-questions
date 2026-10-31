1. Why do you use temperature parameter for your RAG Pipeline?

Ans). temperature parameter use to control the randomness of the LLM's responses. In RAG pipeline, generally configure a low temperature around 0.1 or 0.2, because i want the LLM to generate consistent and factual answers based on the retrieved document context rather the production creative or speculative reponses.

2. What is RAG and How do you implement a RAG pipeline for you AI Powered document intelligence project?

ans). I worked on an AI-powered document intelligence project where users could ask questions and get answers from large collections of PDF documents.

First, I extracted text from PDFs using PyMuPDF and OCR tools. Then I cleaned the text and divided it into smaller chunks.

Next, I generated embeddings and stored them in a vector database like Qdrant.

When users asked questions, the system retrieved relevant information using semantic search and keyword search.

Finally, I passed the retrieved information to an LLM, which generated answers with document names and page numbers.

I also implemented batch processing, parallel execution, and error handling to make the pipeline scalable and efficient.

The main goal was to help users quickly find accurate information from thousands of documents."


3. How would you handle large pdf and how you handle tables, images and charts?

Ans). "In my project, I handled large PDF documents using a batch-based and parallel processing approach instead of processing all documents at once.

First, I stored the PDFs in cloud storage and processed them using background workers. I used tools like PyMuPDF or Docling to extract text from PDF pages.

Since tables, images, and charts were embedded within the PDFs, I handled them during the same PDF processing pipeline.
- Text: I extracted normal text using PyMuPDF or Docling.
- Images: I extracted embedded images and used OCR when they contained text. For complex diagrams and visual content, I used vision-language models.
- Tables: I used table extraction tools to preserve rows, columns, and headers, and converted the extracted data into structured formats such as Markdown or JSON.
- Charts: I used vision models to understand chart titles, labels, legends, and trends, and converted the information into text descriptions.
After extracting all the content, I cleaned it and created meaningful chunks while preserving document IDs, page numbers, and section information.

Then I generated embeddings in batches and stored them in a vector database.

To handle large volumes efficiently, I also used parallel processing, batch embedding, retry mechanisms, and incremental indexing. This helped make the RAG pipeline scalable and reliable.

Finally, when users asked questions, the system retrieved relevant information from the PDFs and generated answers with document and page references."


4. You are using different tools to handle like images ,text, tables and charts. How will you store or make them align as it is in same order?

Ans). Since I use different tools to extract text, tables, images, and charts, I maintain a common document structure to preserve their original order.

For every extracted element, I store the document ID, page number, content type, sequence number, and bounding box coordinates.

After extraction, I sort the elements based on their page number and position. Then I combine related elements, such as headings, paragraphs, tables, and chart descriptions, into meaningful chunks.

I preserve the metadata along with each chunk and generate embeddings. This helps the RAG system retrieve the relevant content while maintaining its original document context and page references.


5. What are the types of retrieval search?

Ans). In RAG, the main retrieval techniques are keyword-based search, semantic search, and hybrid search.

Keyword search uses algorithms like BM25 to find exact matching terms. Semantic search uses embeddings to understand the meaning of the query and retrieve contextually relevant chunks.

Hybrid search combines both approaches to improve retrieval accuracy. We can also apply metadata filtering to restrict results based on document attributes.

After retrieving the chunks, we can use a reranking model to reorder them based on relevance before passing the top results to the LLM.

In my document intelligence project, I would prefer hybrid retrieval with reranking because PDF documents contain both exact information, such as employee IDs and financial figures, and context-based information."

Remember this for interviews:
- Keyword search = Exact matching.
- Semantic search = Meaning matching.
- Hybrid search = Both.
- Reranking = Improve the order of retrieved results.



 example code : multi_query_retriever = MultiQueryRetriever.from_llm(
    retriever=vector_retriever,
    llm=llm
)

6. what is the difference between grounding and guardrails?

Ans). In GenAI and RAG applications, Grounding and Guardrails are two different techniques used to make AI responses more reliable and safe.

The main difference is:
- Grounding: Ensures the AI generates answers based on trusted information or provided context.
- Guardrails: Ensures the AI follows predefined rules, restrictions, and safety policies.

7. What are the challenges did you face while building AI powered document inteligence application?

ans). While building an AI-powered document intelligence application using RAG, I faced several challenges.

**First, handling large PDF documents.** Processing large numbers of PDFs was time-consuming, so I used batch processing, parallel execution, and background workers to improve performance.

**Second, extracting information** from tables, images, and scanned PDFs. Normal text extraction was not sufficient, so I used OCR for scanned documents, table extraction techniques for tables, and vision models for charts and diagrams.

**Third, maintaining context during chunking**. Random chunking sometimes separated headings, tables, and related paragraphs. To solve this, I used document-aware chunking and preserved page numbers and metadata.

**Fourth, retrieval accuracy. **Sometimes the system retrieved irrelevant chunks. I improved this by combining keyword search and semantic search using hybrid retrieval, followed by reranking.

**Fifth, hallucination.** Sometimes the LLM generated answers that were not supported by the documents. I addressed this through grounding, context-based prompts, and source citations.

Finally, I focused on scalability and reliability using batch processing, retries, checkpointing, and incremental indexing.

Overall, the main challenge was not just generating answers using an LLM, but ensuring that the answers were accurate, relevant, scalable, and based on the original documents.

7. How did you improve retrieval accuracy?

ans). To improve retrieval accuracy in my RAG-based document intelligence project, I focused on several areas.

First, I improved the chunking strategy by using document-aware chunking and preserving headings, page numbers, and related content.

Second, I implemented hybrid search by combining BM25 keyword search with semantic vector search. This helped retrieve both exact keyword matches and contextually relevant information.

Third, I applied metadata filtering to restrict searches based on document attributes.

Fourth, I implemented reranking. Initially, I retrieved around 20–30 chunks and used a reranking model to select the top 5–10 most relevant chunks before passing them to the LLM.

I also worked on query rewriting for ambiguous questions.

Finally, I evaluated retrieval performance using metrics like Recall@K, Precision@K, MRR, and NDCG.

These techniques helped improve the relevance of retrieved information and the overall quality of RAG responses.

8. How did you decide the chunk size and overlap?

ans). In my understanding, chunk size determines how much text is stored in each chunk, while chunk overlap is the repeated text shared between consecutive chunks.

If the chunk size is too small, important context may get separated, and the system may retrieve incomplete information. If it is too large, the chunk may contain unnecessary information, which can reduce retrieval accuracy and consume more tokens.

Overlap helps preserve context when a sentence or important information is split between two chunks.

For a general RAG application, I would start with a chunk size of around 500–800 tokens and an overlap of 50–100 tokens. However, these values depend on the document structure and use case.

Finally, I would test different configurations and evaluate retrieval accuracy using metrics like Recall@K, Precision@K, and MRR to choose the best settings.”

Easy way to remember:
- Chunk size: How much information goes into one chunk.
- Overlap: How much information is repeated between chunks.
- Too small: Context may be lost.
- Too large: Irrelevant information may be retrieved.
- Goal: Balance context and retrieval accuracy.

9. How did you reduce hallucinations?

ans). hallucination happens when an LLM generates incorrect or unsupported information. To reduce hallucinations in my RAG-based document intelligence application, I would focus on grounding the LLM with relevant information from the source documents.

First, I use semantic search and keyword-based search to retrieve relevant chunks from the PDF documents. Then I pass only the most relevant retrieved chunks to the LLM as context.

I also use a prompt that instructs the LLM to answer only from the provided context. If the information is not available in the documents, it should clearly say that it doesn't know rather than generating an answer.

Additionally, I use reranking to improve the relevance of retrieved chunks, provide document names and page numbers as citations, and keep the temperature low to make responses more consistent.

Finally, I evaluate the answers for faithfulness and correctness.”

Key techniques to remember:
1. Grounding: Generate answers using retrieved PDF content.
2. Hybrid search: Combine keyword and semantic search.
3. Reranking: Select the most relevant chunks.
4. Prompt instructions: Tell the LLM not to make up answers.
5. Citations: Show document names and page numbers.
6. Low temperature: Reduce randomness in responses.

10. How do you measure retrieval accuracy?
ans). This is another important interview point.
    Metric	Meaning
    Recall@K	Whether the relevant chunks are present in the top K results
    Precision@K	How many of the retrieved chunks are relevant
    MRR	How high the first relevant result appears
    NDCG	Measures ranking quality, considering relevance and position

    For example, if the system retrieves 10 chunks and 8 are relevant:
    \[
    Precision@10 = \frac{8}{10} = 0.8
    \]

    So precision is 80%.

11. What is transformer architecture?

ans). Transformer is a deep learning architecture mainly used in NLP and Generative AI. It is designed to understand relationships between words in a sentence using a mechanism called Self-Attention.

Unlike traditional RNNs and LSTMs, which process text sequentially, Transformers can process multiple tokens in parallel. This makes training faster and helps the model understand long-range relationships between words.

The main components of Transformer architecture are:
1. Input Embedding: Converts words or tokens into numerical vectors.
2. Positional Encoding: Adds information about the position of each token in the sentence.
3. Self-Attention: Helps the model understand how each word is related to other words.
4. Feed Forward Network: Processes the information learned by the attention mechanism.
5. Add & Norm: Uses residual connections and layer normalization to make training more stable.
6. Encoder and Decoder: The encoder understands the input, while the decoder generates the output.
Transformers are the foundation of modern models like GPT, BERT, and T5.

12. How would you handle 100K PDFs in a RAG pipeline?

Interview-ready answer:

“If I had to handle 100K PDFs in a RAG pipeline, I would design a scalable, distributed and asynchronous ingestion architecture rather than processing all documents at once.

I would divide the process into the following steps:

1\. **Document storage and management**

First, I would store all PDF files in object storage like AWS S3 or Azure Blob Storage. I would maintain metadata such as document ID, file name, upload date and document version. I would also use checksums to identify duplicate files.

2\.** Distributed document processing**

I would use a message queue like Kafka, RabbitMQ or AWS SQS to distribute PDF processing tasks among multiple workers.

Each worker would process PDFs independently using tools like PyMuPDF or Docling. For scanned PDFs, I would use OCR tools like Tesseract or AWS Textract.

3\. **Chunking**

After extracting the content, I would clean the text and divide it into meaningful chunks.

I would start with around 500–800 tokens per chunk and 50–100 tokens of overlap, depending on the document structure. I would also preserve page numbers, headings, tables and document IDs.

4\. **Embedding generation**

Next, I would generate embeddings in batches using an embedding model. I would use parallel processing with proper rate limits, retries and error handling to avoid API failures.

5\.** Vector database storage**

I would store the embeddings along with the original text and metadata in a scalable vector database like Qdrant, Milvus or Pinecone.

6\.** Retrieval optimization**

When a user asks a question, I would use hybrid search, combining semantic search and keyword search. Then I would apply reranking to select the most relevant chunks before passing them to the LLM.

7\.** Performance and reliability**

Finally, I would implement batch processing, checkpointing, logging, retry mechanisms and incremental indexing. This ensures that if processing fails, I don't need to process all 100K PDFs again. I would also monitor ingestion time, query latency, retrieval accuracy and processing failures.”

## Architecture flow

100K PDF Documents

Object Storage (AWS S3 / Azure Blob)

Message Queue (Kafka / SQS)

Parallel Processing Workers

PDF Extraction

OCR Processing

Cleaning

Chunking

Batch Embedding Generation

Vector Database (Qdrant / Milvus)

User Query → Hybrid Search → Reranking → LLM → Answer with Citations

## Important points interviewers may ask

1\. **Why use a message queue?**

To distribute PDF processing tasks across multiple workers and prevent one process from handling all documents.

2\. **Why use batch processing?**

To process multiple documents or chunks efficiently instead of making individual embedding requests for everything.

3\. **What happens if a worker fails?**

I would use retries, checkpointing and idempotent processing so failed documents can be processed again without duplicating existing records.

4\. **Would you use LangGraph for processing 100K PDFs?**

Not for the entire bulk ingestion pipeline. I would use distributed workers and a message queue for ingestion. LangGraph can be useful for query-time agent workflows involving multiple retrieval or reasoning steps.

5\.** How would you improve retrieval accuracy?**

I would combine BM25 keyword search with vector search, use reranking, apply metadata filters and evaluate using Recall\@K, Precision\@K and MRR.

Remember this interview flow:

Storage → Queue → Parallel Processing → Extraction/OCR → Chunking → Embeddings → Vector DB → Hybrid Retrieval → Reranking → LLM.

One important point: These are proposed architecture choices. In an interview, mention only the tools and implementation details you actually used as your project experience.