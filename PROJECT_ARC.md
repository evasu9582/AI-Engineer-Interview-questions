# AI Engineering Project Architecture

This document provides practical architecture explanations for two interview
scenarios:

1. **Life insurance underwriting fraud detection** using LangChain and LangGraph
2. **Layout-aware document processing for RAG ingestion** using Azure services

## Table of contents

- [1. Life insurance underwriting fraud detection](#1-life-insurance-underwriting-fraud-detection)
  - [Overall architecture](#overall-architecture)
  - [Verification agent](#verification-agent)
  - [Document forgery detection agent](#document-forgery-detection-agent)
  - [Domain knowledge agent](#domain-knowledge-agent)
  - [Fraud analysis agent](#fraud-analysis-agent)
  - [Agent communication](#agent-communication)
  - [Agent comparison](#agent-comparison)
  - [Interview-ready summary](#interview-ready-summary)
  - [Likely follow-up questions](#likely-follow-up-questions)
- [2. Layout-aware document processing for RAG](#2-layout-aware-document-processing-for-rag)
  - [Interview-ready answer](#interview-ready-answer)
  - [Architecture](#architecture)
  - [Element-specific processing](#element-specific-processing)
  - [Processing 100,000 PDFs](#processing-100000-pdfs)
  - [Metadata strategy](#metadata-strategy)
  - [Follow-up questions](#follow-up-questions)
  - [Actuarial insurance example](#actuarial-insurance-example)

---

# 1. Life insurance underwriting fraud detection

The system uses four specialized agents. Each agent performs a specific task
with tools, databases, business rules, OCR, or AI models. The agents do not
simply ask an LLM to answer, “Is this fraud?”

| Agent | Primary responsibility |
| --- | --- |
| Verification | Validate applicant information against trusted records |
| Forgery detection | Check whether a submitted document appears manipulated |
| Domain knowledge | Apply approved insurance rules and guidelines |
| Fraud analysis | Correlate findings and prioritize investigation |

## Overall architecture

```text
Applicant
  |
  | Application and supporting documents
  v
LangGraph supervisor / orchestrator
  |
  +--> Verification agent
  |       Identity and data validation
  |
  +--> Forgery detection agent
  |       Document authenticity analysis
  |
  +--> Domain knowledge agent
  |       Underwriting rules and policy guidelines
  |
  +--> Fraud analysis agent
          Correlation and risk prioritization
  |
  v
Evidence-based investigation report
  |
  v
Human underwriter or fraud investigation team
```

### Example applicant

| Field | Declared value |
| --- | --- |
| Name | Rahul Kumar |
| Date of birth | 15-05-1990 |
| PAN | ABCDE1234F |
| Annual income | ₹5 lakh |
| Existing policies | 1 |

---

## Verification agent

**Purpose:** Determine whether applicant information is consistent with trusted
records and submitted evidence.

### 1. Extract information

Use OCR or document intelligence to extract applicant data.

```python
applicant_data = {
    "name": "Rahul Kumar",
    "dob": "1990-05-15",
    "pan": "ABCDE1234F",
    "income": 500000,
}
```

### 2. Validate information

The agent invokes Python tools and authorized verification services:

- Verify PAN information through an authorized provider.
- Compare names and dates of birth across documents.
- Compare declared income with salary slips and bank records.
- Check existing policies in internal databases.

### 3. Identify mismatches

For example, the extracted salary slip may show ₹15 lakh, while the application
declares ₹5 lakh.

```python
verification_result = {
    "identity_status": "verified",
    "income_status": "mismatch",
    "declared_income": 500000,
    "document_income": 1500000,
    "flag": True,
}
```

An inconsistency indicates the need for investigation; it does **not** prove
fraud.

### Technologies

- Python
- OCR or Azure AI Document Intelligence
- SQL and PostgreSQL
- FastAPI
- LangChain tools
- Authorized verification APIs

---

## Document forgery detection agent

**Purpose:** Determine whether a document shows signs of tampering,
manipulation, duplication, or fabrication.

| Question | Verification agent | Forgery detection agent |
| --- | --- | --- |
| What is checked? | Whether applicant data is correct | Whether the document itself is authentic |
| Example | Does the PAN match the applicant? | Was the salary field edited? |

For a salary slip, the agent checks whether an amount was changed from ₹5 lakh
to ₹15 lakh or whether the document was copied or altered.

### Detection techniques

| Technique | What it detects |
| --- | --- |
| Image tampering detection | Edited amounts, names, or dates |
| Font analysis | Font or size differences in edited areas |
| Metadata analysis | Unexpected editing software or modification history |
| Digital signature validation | Invalid or broken signatures |
| Duplicate detection | Reused documents across applications |
| OCR consistency | Differences between extracted text and document structure |

### Technical workflow

#### Step 1: Preprocess the document

```python
import cv2

image = cv2.imread("salary_slip.jpg")
gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
processed = cv2.threshold(
    gray,
    150,
    255,
    cv2.THRESH_BINARY,
)[1]
```

#### Step 2: Extract document content

```python
extracted_data = {
    "employee_name": "Rahul Kumar",
    "company": "ABC Technologies",
    "monthly_salary": 125000,
    "date": "2026-10-01",
}
```

#### Step 3: Analyze the image

Look for:

- Inconsistent pixel patterns
- Copy-move manipulation
- Altered text regions
- Unusual image compression
- Digital editing artifacts

OpenCV supports general image analysis, but sophisticated forgery detection
normally requires specialized forensic models and validation.

#### Step 4: Produce a structured result

```python
forgery_result = {
    "document_type": "salary_slip",
    "tampering_detected": True,
    "suspicious_fields": ["monthly_salary"],
    "evidence": "Possible inconsistent text region",
    "confidence": 0.89,
    "status": "manual_review",
}
```

The confidence value must come from a calibrated model in production. An LLM
should not assign or self-report model confidence.

> **Interview point:** An LLM alone cannot reliably detect image forgery.
> Computer vision, OCR, signature validation, and forensic models perform the
> detection; the LLM orchestrates the tools and explains the evidence.

---

## Domain knowledge agent

**Purpose:** Interpret approved underwriting guidelines, policy rules,
eligibility criteria, and fraud investigation procedures.

The applicant may declare:

- Age: 55 years
- Annual income: ₹5 lakh
- Requested sum assured: ₹2 crore

The agent retrieves applicable guidelines and determines whether additional
financial justification is required.

```text
Applicant information and flagged findings
                    |
                    v
Domain knowledge agent
                    |
                    v
LangChain RAG retriever
                    |
                    v
Vector database
Pinecone / ChromaDB
                    |
                    v
Underwriting guidelines
Income eligibility / age limits / sum assured rules
```

### Prepare the knowledge base

Index approved sources only:

- Underwriting guideline PDFs
- Insurance policy documents
- Internal underwriting procedures
- Approved fraud investigation guidelines

### Retrieve relevant rules

```python
from langchain_core.prompts import ChatPromptTemplate

retriever = vectorstore.as_retriever(
    search_kwargs={"k": 5},
)

query = """
Check underwriting requirements for:
Age 55,
annual income 5 lakh,
sum assured 2 crore.
"""

docs = retriever.invoke(query)
context = "\n".join(doc.page_content for doc in docs)

prompt = ChatPromptTemplate.from_template(
    """
You are an insurance domain expert.

Analyze the applicant using the following underwriting guidelines:
{context}

Applicant details:
{question}

Return applicable rules and supporting evidence.
Do not invent underwriting rules.
"""
)
```

Example result:

```python
domain_result = {
    "applicable_rule": "Income-based eligibility",
    "finding": "Additional financial justification required",
    "source": "Underwriting guideline, page 12",
    "status": "review_required",
}
```

The final eligibility threshold must come from approved organizational rules,
not an LLM-generated rule.

### Technologies

- LangChain
- Azure OpenAI
- RAG
- Azure OpenAI embeddings
- Pinecone or ChromaDB
- Hybrid search
- PostgreSQL

---

## Fraud analysis agent

**Purpose:** Correlate the findings from the other agents and prioritize
cases for investigation.

### Findings for Rahul Kumar

| Agent | Result |
| --- | --- |
| Verification | Declared income is ₹5 lakh, but the salary document shows ₹15 lakh |
| Forgery detection | Possible manipulation around the salary field |
| Domain knowledge | Additional financial justification is required |

### Workflow

#### 1. Collect findings

Read the structured outputs produced by verification, forgery detection, and
domain analysis.

#### 2. Correlate findings

Check whether independent signals indicate the same suspicious behavior:

- Income declaration mismatch
- Possible document manipulation
- Inconsistency with underwriting requirements

#### 3. Apply deterministic rules

The following example is simplified and is not a validated fraud scoring
model:

```python
def analyze_fraud(verification, forgery, domain):
    risk_flags = []

    if verification["income_status"] == "mismatch":
        risk_flags.append("INCOME_MISMATCH")

    if forgery["tampering_detected"]:
        risk_flags.append("POSSIBLE_DOCUMENT_TAMPERING")

    if domain["status"] == "review_required":
        risk_flags.append("UNDERWRITING_REVIEW")

    if len(risk_flags) >= 2:
        investigation_status = "high_priority_investigation"
    elif len(risk_flags) == 1:
        investigation_status = "further_review"
    else:
        investigation_status = "no_major_anomaly_detected"

    return {
        "risk_flags": risk_flags,
        "investigation_status": investigation_status,
    }
```

Production fraud scoring should account for evidence quality, false-positive
rates, calibrated thresholds, and independent verification.

#### 4. Generate an investigation report

```python
fraud_report = {
    "applicant": "Rahul Kumar",
    "risk_indicators": [
        "Income declaration mismatch",
        "Possible salary slip manipulation",
        "Additional financial justification required",
    ],
    "investigation_status": "high_priority_investigation",
    "recommended_action": "Refer to human fraud investigation team",
}
```

The final result is a recommendation for investigation, not an automatic
finding of fraud.

---

## Agent communication

LangGraph stores agent outputs in shared state:

```python
from typing import TypedDict


class FraudState(TypedDict):
    applicant_data: dict
    verification_result: dict
    forgery_result: dict
    domain_result: dict
    fraud_result: dict
    final_status: str
```

Each agent becomes a LangGraph node:

```python
from langgraph.graph import StateGraph, END


def verification_agent(state):
    result = verify_applicant(state["applicant_data"])
    return {"verification_result": result}


def forgery_agent(state):
    result = detect_forgery(state["applicant_data"])
    return {"forgery_result": result}


def domain_agent(state):
    result = analyze_underwriting_rules(state["applicant_data"])
    return {"domain_result": result}


def fraud_agent(state):
    result = analyze_fraud(
        state["verification_result"],
        state["forgery_result"],
        state["domain_result"],
    )
    return {"fraud_result": result}
```

Build the workflow:

```python
workflow = StateGraph(FraudState)

workflow.add_node("verification", verification_agent)
workflow.add_node("forgery", forgery_agent)
workflow.add_node("domain", domain_agent)
workflow.add_node("fraud", fraud_agent)

workflow.set_entry_point("verification")
workflow.add_edge("verification", "forgery")
workflow.add_edge("forgery", "domain")
workflow.add_edge("domain", "fraud")
workflow.add_edge("fraud", END)

app = workflow.compile()
```

The sequential example is easy to explain. In production, verification,
forgery, and domain analysis can run in parallel after the applicant data is
available. Fraud analysis should run only after those branches complete.

---

## Agent comparison

| Agent | Main question |
| --- | --- |
| Verification | Is the applicant's information correct and consistent? |
| Forgery detection | Is a submitted document potentially manipulated? |
| Domain knowledge | Does the application comply with approved guidelines? |
| Fraud analysis | Do multiple findings indicate activity requiring investigation? |

## Interview-ready summary

> I would design the underwriting fraud detection system as a multi-agent
> architecture orchestrated by LangGraph.
>
> The Verification Agent validates applicant information against internal
> databases and authorized external services. The Forgery Detection Agent uses
> OCR, OpenCV, and specialized forensic models to detect possible document
> manipulation. The Domain Knowledge Agent uses RAG to retrieve approved
> underwriting guidelines and eligibility criteria. The Fraud Analysis Agent
> correlates the findings, applies deterministic fraud rules, and prioritizes
> the case for human review.
>
> LangGraph manages shared state and conditional workflows. LangChain integrates
> LLMs, tools, database queries, and vector retrieval. The final output is an
> evidence-based investigation recommendation, not an automatic fraud decision.

## Likely follow-up questions

1. How does the Fraud Analysis Agent identify fraud without a predefined rule?
2. How do you detect forged salary slips using OpenCV and AI models?
3. How does the Domain Agent retrieve underwriting guidelines using RAG?
4. How do agents share information and pass outputs in LangGraph?
5. How do you implement human-in-the-loop approval?

---

# 2. Layout-aware document processing for RAG

## Interview-ready answer

> In a RAG application, layout-aware document processing is necessary when PDFs
> contain paragraphs, tables, charts, images, headers, footnotes, and
> multi-column layouts. Plain-text extraction can lose relationships between
> table headers and values or between a chart and its labels.
>
> I would use Azure AI Document Intelligence with the prebuilt-layout model to
> analyze the PDF. The pipeline would upload the document, extract layout
> elements, classify and process each element according to its type, create
> meaningful chunks, attach metadata, generate embeddings, and store them in a
> vector database.
>
> For retrieval, semantic search finds conceptually relevant content, while
> keyword or hybrid search finds exact terms and identifiers. The LLM receives
> the retrieved context and generates a grounded answer with document and page
> references.

## Architecture

```text
PDF document
  |
  v
Azure Blob Storage
  |
  v
Azure AI Document Intelligence
OCR, paragraphs, tables, figures, page structure
  |
  v
Element classification
  +--> Text --> Recursive or semantic chunking
  +--> Tables --> Preserve headers and row-column relationships
  +--> Charts --> Vision extraction and validation
  +--> Images --> Vision description and original image reference
  |
  v
Chunking and metadata
  |
  v
Azure OpenAI embeddings
  |
  v
Pinecone or Azure AI Search
  |
  v
Hybrid retrieval + reranking + LLM
```

The ingestion stages are:

1. **Document upload:** Store the PDF in Azure Blob Storage and assign a unique
   document ID.
2. **Layout analysis:** Extract text, paragraphs, tables, figures, page
   numbers, bounding regions, and reading order.
3. **Element classification:** Identify text, table, chart, image, header,
   footer, or other elements.
4. **Element-specific processing:** Apply a suitable processing strategy to
   each element.
5. **Chunk creation:** Create meaningful chunks based on document structure.
6. **Metadata attachment:** Preserve document, page, section, element, and
   parent-chunk information.
7. **Embedding generation:** Convert searchable text into vectors.
8. **Vector storage:** Store vectors, text, and metadata.
9. **Retrieval and generation:** Find relevant chunks and produce a grounded
   response.

---

## Element-specific processing

### A. Process text

```text
"Life insurance policies provide financial protection to policyholders.
The premium depends on age, policy term, and sum assured."
```

Use recursive character splitting for long prose:

```python
from langchain_text_splitters import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(
    chunk_size=800,
    chunk_overlap=150,
)

chunks = splitter.split_text(document_text)
```

- `chunk_size=800` sets the target chunk length.
- `chunk_overlap=150` repeats a limited amount of adjacent text.
- Overlap helps preserve context across topic or sentence boundaries.

### B. Process tables

Consider:

| Age | Premium | Sum Assured |
| --- | --- | --- |
| 25 | 5,000 | 500,000 |
| 35 | 7,000 | 500,000 |
| 45 | 10,000 | 500,000 |

Convert the table to a searchable representation that retains its headers:

```markdown
Table: Annual premium based on age

| Age | Premium | Sum Assured |
| --- | --- | --- |
| 25 | 5000 | 500000 |
| 35 | 7000 | 500000 |
| 45 | 10000 | 500000 |
```

For a large table, split by row groups and repeat the headers in every chunk.
Keep the table title and source metadata with each chunk.

> **Interview point:** Table-aware chunking preserves relationships between
> columns and values.

### C. Process charts

A chart may contain:

- X-axis: age
- Y-axis: annual premium
- Legend: policy type
- Data points: premium values

The chart-processing workflow is:

1. Extract the chart or figure from the PDF.
2. Store the original image in Azure Blob Storage.
3. Send the image to a vision-capable model.
4. Extract the chart title, axes, labels, units, and visible values.
5. Validate extracted values where possible.
6. Store the structured description and a reference to the original chart.

Example searchable description:

```text
Chart title: Annual premium by age

The chart compares annual premiums for different age groups.
The premium increases as the policyholder's age increases.
Source: Document ID DOC101, Page 12.
```

A vision model can misread chart values. Numerical interpretation should not be
treated as verified data without validation.

### D. Process images

For an image:

1. Extract the image.
2. Store it separately in Azure Blob Storage.
3. Generate a meaningful description with a vision model.
4. Store the description as searchable text.
5. Keep the original image reference and page number.

Example:

```text
Image description:
The image shows the underwriting verification workflow with document
validation and approval steps.
```

The description can be embedded for retrieval while the original image remains
available for inspection.

---

## Processing 100,000 PDFs

Do not process 100,000 PDFs sequentially in a single script. Use an asynchronous,
distributed ingestion pipeline:

```text
Azure Blob Storage
  100,000 PDFs
       |
       v
Azure Service Bus
  Document processing jobs
       |
       +--> Worker 1 --> Chunk
       +--> Worker 2 --> Embed
       +--> Worker N --> Index
       |
       v
Pinecone / Azure AI Search
```

Recommended design considerations:

1. **Asynchronous processing:** Queue documents rather than blocking API requests.
2. **Parallel workers:** Process multiple documents concurrently.
3. **Batch embeddings:** Generate embeddings within model limits.
4. **Retry policy:** Retry transient API failures.
5. **Dead-letter queue:** Retain documents that repeatedly fail.
6. **Idempotency:** Prevent duplicate indexing.
7. **Incremental ingestion:** Process only new or modified documents.
8. **Monitoring:** Track latency, failures, token usage, and indexing status.

Worker count and concurrency should depend on Azure quotas, document size, and
acceptable processing latency.

---

## Metadata strategy

Example metadata for a table chunk:

```python
metadata = {
    "document_id": "DOC101",
    "chunk_id": "CHUNK001",
    "element_type": "table",
    "page_number": 12,
    "section": "Premium Calculation",
    "parent_id": "SECTION001",
    "source": "actuarial_guidelines.pdf",
}
```

| Metadata | Purpose |
| --- | --- |
| `document_id` | Identify the original document |
| `chunk_id` | Identify an individual chunk |
| `element_type` | Distinguish text, table, chart, or image |
| `page_number` | Support source citations |
| `section` | Preserve document context |
| `parent_id` | Connect child chunks to parent sections |
| `source` | Identify the original PDF |

Metadata supports filtering, access control, traceability, and source citations.

---

## Follow-up questions

### Why not use normal text extraction?

Normal extraction may lose layout, reading order, table relationships, image
context, headers, and footnotes. Layout-aware extraction preserves more of the
document structure.

### Is Azure AI Document Intelligence an embedding model?

No. Azure AI Document Intelligence extracts and analyzes document content.
Azure OpenAI embedding models convert searchable text into numerical vectors.

### Does Azure AI Document Intelligence understand charts?

It can identify and extract figure information, but detailed chart interpretation
usually requires a vision-capable model and validation.

### Layout-aware chunking versus semantic chunking

| Approach | Basis |
| --- | --- |
| Layout-aware | Headings, tables, figures, and page boundaries |
| Semantic | Meaning and topic boundaries |

The approaches can be combined.

### Why use hybrid search?

Vector search retrieves semantically similar content. Keyword search retrieves
exact terms, identifiers, and policy numbers. Hybrid search combines both.

### How do you retain context in table chunks?

Repeat table headers, preserve row-column relationships, include table titles,
and retain page and section metadata.

### How do you avoid duplicate documents?

Use document hashes, version tracking, and idempotent ingestion jobs. Re-index
a document only when its content changes.

---

## Key concepts

| Technology or concept | Responsibility |
| --- | --- |
| Azure Blob Storage | Store PDFs and extracted images |
| Azure AI Document Intelligence | Extract layout and content |
| OCR | Extract text from scanned pages |
| Layout-aware chunking | Preserve document structure |
| Recursive chunking | Split long prose into manageable chunks |
| Table-aware chunking | Preserve table relationships |
| Vision model | Interpret images and charts |
| Azure OpenAI embeddings | Convert text into vectors |
| Pinecone or Azure AI Search | Store and retrieve vectors |
| LangChain | Integrate LLMs, embeddings, and retrievers |
| LangGraph | Orchestrate multi-step or agentic workflows |
| Azure Service Bus | Queue distributed ingestion jobs |

## Actuarial insurance example

Suppose an actuary uploads a guideline PDF containing premium rules, age-based
tables, and charts.

1. Store the PDF in Azure Blob Storage.
2. Use Azure AI Document Intelligence to extract text, tables, figures, and
   layout.
3. Process text with recursive or semantic chunking.
4. Convert tables to structured Markdown while preserving headers.
5. Send charts to a vision model and validate extracted values.
6. Attach metadata such as document ID, page number, section, and element type.
7. Generate embeddings with Azure OpenAI and store them in the vector database.

When the actuary asks, “What is the premium for a 35-year-old policyholder?”
the retrieval system searches relevant premium tables and guideline sections.
The LLM uses that context to answer with document and page references.

## Interview checklist

Be able to explain these three stages clearly:

1. **Document intelligence:** How do you extract content from a complex PDF?
2. **Layout-aware chunking:** How do you split content without losing structure?
3. **RAG retrieval:** How do you retrieve the correct information for a query?
