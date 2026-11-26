# Life Insurance Underwriting Fraud Detection

## Complete Project Interview Questions and Answers

This client-round preparation guide covers the project explanation, architecture, four agents, GenAI, Agentic AI, prompts, LangGraph, RAG, OCR, OpenCV, Python, SQL, and common scenario-based questions.

> **Key interview point:** The project does not use an LLM for every operation. Python, SQL, OCR, OpenCV, and specialized forensic models perform extraction, validation, and detection. LLMs interpret findings, correlate evidence, and explain results. LangGraph orchestrates the workflow.

## Contents

1. [Project Introduction and Architecture](#1-project-introduction-and-architecture)
2. [Verification Agent](#2-verification-agent)
3. [Forgery Detection Agent](#3-forgery-detection-agent)
4. [Domain Knowledge Agent and RAG](#4-domain-knowledge-agent-and-rag)
5. [Fraud Analysis Agent](#5-fraud-analysis-agent)
6. [GenAI and Agentic AI](#6-genai-and-agentic-ai-in-your-project)
7. [Prompts for All Four Agents](#7-prompts-for-all-four-agents)
8. [Python, FastAPI, SQL, and Backend](#8-python-fastapi-sql-and-backend-questions)
9. [Deployment, Performance, and Production](#9-deployment-performance-and-production-questions)
10. [Real-Time Project Scenarios](#10-real-time-project-scenario-questions)
11. [Additional Agentic AI and LLM Questions](#11-additional-agentic-ai-and-llm-questions)
12. [Individual Contribution](#12-interviewer-may-ask-what-was-your-individual-contribution)
13. [Technology Stack](#13-technology-stack--remember-this-table)
14. [Final 2-Minute Explanation](#14-final-2-minute-project-explanation)
15. [Interview Preparation Priorities](#15-what-you-should-prioritize-before-the-interview)

---

## 1. Project Introduction and Architecture

### **Q1. Explain your project.**

**Answer:**

"Sir, I worked on a Life Insurance Underwriting Fraud Detection system. The main objective of this system is to identify inconsistencies, suspicious documents, and potential fraud indicators during the life insurance underwriting process.

When an applicant applies for life insurance, they submit documents such as identity proof, PAN, salary slips, bank statements, and other supporting documents.

Manually verifying all these documents is time-consuming. So, we designed a multi-agent AI architecture using Python, LangChain, LangGraph, OCR, OpenCV, SQL, and Azure OpenAI.

Our system consists of four specialized agents:

1. Verification Agent – validates applicant information against trusted records.
2. Forgery Detection Agent – identifies potential document manipulation.
3. Domain Knowledge Agent – retrieves and interprets approved underwriting guidelines using RAG.
4. Fraud Analysis Agent – correlates the findings from all agents and prioritizes cases for investigation.

We used LangGraph to orchestrate these agents and manage shared state. The system generates an evidence-based investigation report, which is reviewed by a human underwriter or fraud investigation team."

### **Q2. Explain the end-to-end architecture.**

**Answer:**

The workflow is:

**1. Applicant submits application and documents**

PAN, identity proof, salary slip, bank statement

**2. LangGraph Supervisor / Orchestrator**

Initializes state and coordinates agents

**Verification Agent**

OCR, SQL, Python, APIs

**Forgery Agent**

OCR, OpenCV, forensic tools

**Domain Agent**

RAG, Azure OpenAI, vector DB

**Fraud Analysis Agent**

Python rules, LLM analysis

**3. Evidence-based investigation report**

**4. Human underwriter / fraud investigation team**

Reviews findings and makes the final decision

The first three agents produce structured results. The Fraud Analysis Agent consumes those results and prepares the investigation recommendation.

### **Q3. Why did you choose a multi-agent architecture?**

**Answer:**

"We divided the business problem into smaller responsibilities. Document verification, document authenticity, insurance rule interpretation, and fraud investigation require different tools and expertise.

Instead of building one large LLM prompt, we created specialized agents with separate responsibilities, instructions, and tools.

This improves modularity, maintainability, traceability, and the ability to test each stage independently."

### **Q4. Why did you choose LangGraph instead of only LangChain?**

**Answer:**

"LangChain helps us integrate LLMs, prompts, retrievers, and external tools. LangGraph helps us manage the complete agent workflow.

We needed shared state, multiple agents, workflow dependencies, conditional routing, and human approval.

LangGraph provides nodes, edges, conditional edges, state management, checkpointing, and interrupt/resume capabilities. That makes it suitable for a multi-agent underwriting workflow."

### **Q5. What is the difference between LangChain and LangGraph?**

| LangChain                          | LangGraph                                  |
| ---------------------------------- | ------------------------------------------ |
| LLM and tool integration framework | Workflow and agent orchestration framework |
| Prompt templates                   | Nodes and edges                            |
| Retrievers and chains              | Shared graph state                         |
| Tool integration                   | Conditional routing                        |
| LLM invocation                     | Multi-step and multi-agent workflows       |

## **2. Verification Agent**

### **Q6. What is the responsibility of the Verification Agent?**

**Answer:**

"The Verification Agent checks whether applicant information is correct and consistent with the submitted documents, internal databases, and authorized external verification services.

For example, it verifies PAN details, compares names and dates of birth across documents, checks declared income against salary slips and bank statements, and checks existing insurance policies."

### **Q7. How did you implement the Verification Agent?**

**Answer:**

"We divided the process into four steps.

1. OCR or Azure AI Document Intelligence extracts applicant information from uploaded documents.
2. Python processes the extracted fields and performs validations.
3. SQL retrieves existing applicant and policy information from PostgreSQL. Authorized external APIs can be used for PAN or other identity verification.
4. The agent produces a structured verification result, which can be interpreted by the LLM and passed to the Fraud Analysis Agent."

Example:

```python
applicant_data = {
    "name": "Rahul Kumar",
    "dob": "1990-05-15",
    "pan": "ABCDE1234F",
    "income": 500000
}
```

Suppose the salary slip indicates ₹15 lakh.

```python
verification_result = {
    "identity_status": "verified",
    "income_status": "mismatch",
    "declared_income": 500000,
    "document_income": 1500000,
    "flag": True
}
```

### **Q8. Why do you need an LLM if Python and SQL can perform verification?**

**Answer:**

"Python and SQL perform deterministic validations and comparisons. OCR extracts the information. The LLM is useful when we need to interpret multiple findings in context and explain the discrepancies.

For example, Python identifies that the declared income is ₹5 lakh and the salary document shows ₹15 lakh. The LLM can generate a contextual explanation and identify what additional evidence may be needed.

We don't use the LLM to replace the actual verification logic. We use it for contextual interpretation and explanation."

### **Q9. How do you handle mismatches between documents?**

**Answer:**

"First, we normalize extracted values, such as names, dates, and income formats. Then Python compares the fields across documents and trusted records.

We classify the results as verified, mismatch, missing, or requiring further review. We retain the source document and field-level evidence so that the investigation team can trace the mismatch.

A mismatch is an investigation signal, not automatic proof of fraud."

## **3. Forgery Detection Agent**

### **Q10. What is the responsibility of the Forgery Detection Agent?**

**Answer:**

"The Forgery Detection Agent checks whether submitted documents show signs of tampering, manipulation, duplication, or fabrication.

For example, an applicant might submit a salary slip in which the salary amount, employee name, or date has been edited.

We use OCR, OpenCV, image analysis, metadata analysis, digital signature validation, and specialized forensic techniques to identify potential anomalies."

### **Q11. How do you detect a forged salary slip?**

**Answer:**

"We follow a document analysis pipeline.

1. First, we preprocess the uploaded image using OpenCV.
2. We extract text and salary details using OCR.
3. We analyze the image for unusual text regions, pixel patterns, copy-move manipulation, and editing artifacts.
4. We examine metadata and validate digital signatures where available.
5. We use Python to organize the forensic findings into a structured result.
6. The LLM interprets the tool outputs and generates an explanation for the investigation team."

Example:

```python
import cv2

image = cv2.imread("salary_slip.jpg")
gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
processed = cv2.threshold(
    gray,
    150,
    255,
    cv2.THRESH_BINARY
)[1]
```

This is preprocessing, not a complete forgery detector. Sophisticated forgery detection requires specialized forensic models and validation.

### **Q12. What is the difference between Verification and Forgery Detection?**

| Verification Agent                              | Forgery Detection Agent                                      |
| ----------------------------------------------- | ------------------------------------------------------------ |
| Checks whether applicant information is correct | Checks whether the document itself may be manipulated        |
| Compares PAN, name, DOB, and income             | Checks edited salary fields, image artifacts, and signatures |
| Uses OCR, SQL, Python, APIs                     | Uses OCR, OpenCV, forensic models                            |
| Finds data inconsistencies                      | Finds document authenticity indicators                       |

**Example:** A salary slip may contain a salary of ₹15 lakh, but the applicant declares ₹5 lakh. Verification identifies the income mismatch. Forgery Detection investigates whether the salary slip itself has been manipulated.

### **Q13. Why do you need an LLM in Forgery Detection?**

**Answer:**

"OpenCV and forensic models perform the actual image analysis. OCR extracts the document content. Python processes the detection results.

The LLM helps interpret the structured forensic output, explain suspicious fields, and prepare an investigation summary.

An LLM alone cannot reliably determine whether an image has been forged. We rely on image analysis and specialized forensic techniques for the actual detection."

### **Q14. Can OpenCV detect all types of document forgery?**

**Answer:**

"No. OpenCV provides image-processing capabilities such as preprocessing, thresholding, edge detection, and region analysis. It does not independently detect every type of sophisticated forgery.

For advanced detection, we may need specialized image-forensics models, metadata inspection, digital signature validation, and human review."

## **4. Domain Knowledge Agent and RAG**

### **Q15. What is the responsibility of the Domain Knowledge Agent?**

**Answer:**

"The Domain Knowledge Agent interprets approved underwriting guidelines, policy documents, eligibility criteria, and internal insurance procedures.

For example, suppose an applicant is 55 years old, declares ₹5 lakh annual income, and requests a sum assured of ₹2 crore.

The agent retrieves the applicable underwriting guidelines and checks whether additional financial justification is required."

### **Q16. How did you implement RAG in the Domain Knowledge Agent?**

**Answer:**

"We implemented Retrieval-Augmented Generation using LangChain, Azure OpenAI embeddings, and a vector database such as Pinecone or ChromaDB.

The process has two main stages.

**Ingestion:**

- Collect approved underwriting guideline PDFs and policy documents.
- Extract document text and relevant content.
- Split the documents into chunks.
- Generate embeddings using Azure OpenAI.
- Store embeddings with metadata in the vector database.

**Retrieval and generation:**

- Receive the applicant's question or flagged findings.
- Convert the query into an embedding.
- Retrieve relevant guideline chunks.
- Pass the retrieved context to the LLM.
- Generate an answer grounded in the approved guidelines, including source references."

### **Q17. What are embeddings?**

**Answer:**

"Embeddings are numerical vector representations of text. They capture semantic information so that similar meanings can be identified even when exact keywords are different.

For example, 'annual salary' and 'yearly income' may have different words but similar semantic meanings.

We use embeddings to retrieve relevant underwriting guidelines based on meaning rather than only exact keyword matching."

### **Q18. What is the difference between keyword search and semantic search?**

**Answer:**

"Keyword search looks for exact words or matching terms. Semantic search identifies content based on meaning and context.

For example, if a user searches for 'yearly earnings', semantic search can retrieve a document section containing 'annual income', even if the exact keyword is different."

### **Q19. Why do we need a vector database?**

**Answer:**

"A vector database stores document embeddings and allows us to perform similarity searches efficiently.

We used Pinecone or ChromaDB to retrieve relevant underwriting guideline chunks based on semantic similarity."

### **Q20. How do you prevent hallucinations in the Domain Knowledge Agent?**

**Answer:**

"We use a grounded prompt that instructs the LLM to answer only using retrieved underwriting guidelines.

We also provide source references and page numbers, validate the generated response, and instruct the model to say when the required rule is unavailable.

Most importantly, actual eligibility thresholds and underwriting decisions come from approved organizational rules, not from an LLM-generated assumption."

### **Q21. What is chunking and why is it needed?**

**Answer:**

"Chunking is the process of splitting large documents into smaller text sections before generating embeddings.

We use it because sending an entire large PDF to the LLM is inefficient and may exceed the model's context limit.

Smaller chunks improve retrieval relevance and allow us to return only the information needed for the user's query."

## **5. Fraud Analysis Agent**

### **Q22. What is the responsibility of the Fraud Analysis Agent?**

**Answer:**

"The Fraud Analysis Agent receives the structured outputs from the Verification Agent, Forgery Detection Agent, and Domain Knowledge Agent.

It correlates the findings, identifies multiple risk indicators, applies predefined business rules, and prioritizes cases for further investigation.

Its purpose is not to automatically declare an applicant fraudulent. It prepares an evidence-based investigation recommendation for the human team."

### **Q23. How does the Fraud Analysis Agent identify fraud?**

**Answer:**

"First, it collects the outputs from all three agents.

For example:

| Agent             | Finding                                           |
| ----------------- | ------------------------------------------------- |
| Verification      | Declared income ₹5 lakh, salary document ₹15 lakh |
| Forgery Detection | Possible manipulation around salary field         |
| Domain Knowledge  | Additional financial justification required       |

Then Python applies deterministic rules to identify and prioritize the risk indicators. The LLM can interpret the combined findings and generate a human-readable investigation report.

If multiple independent indicators are present, the case can be prioritized for investigation."

### **Q24. How do agents communicate with each other?**

**Answer:**

"We use LangGraph shared state. Each agent is implemented as a node in the graph.

The Verification Agent writes its output into `verification_result`. The Forgery Detection Agent writes into `forgery_result`. The Domain Knowledge Agent writes into `domain_result`.

The Fraud Analysis Agent reads all these results from the shared state and produces the final investigation recommendation."

### **Q25. What is LangGraph state?**

**Answer:**

"State is a shared data structure that stores information throughout the workflow. It allows different agents to access the applicant information and previous agent outputs."

Example:

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

Each node returns its updates:

```python
def verification_agent(state):
    result = verify_applicant(state["applicant_data"])
    return {
        "verification_result": result
    }
```

### **Q26. How did you build the workflow in LangGraph?**

**Answer:**

"We created individual nodes for the four agents and connected them through graph edges.

The simple workflow can be sequential. In a production design, the Verification, Forgery Detection, and Domain Knowledge agents can run in parallel once applicant information is available. The Fraud Analysis Agent runs after their results are ready."

Simplified sequential example:

```python
from langgraph.graph import StateGraph, END

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

### **Q27. What is conditional routing in LangGraph?**

**Answer:**

"Conditional routing means the next node is selected based on the current state or the output of an agent.

For example, if the Verification Agent detects a missing identity document, we can route the application to an additional verification or manual review step. If all required information is available, the workflow can continue.

This makes the workflow dynamic rather than always following the same sequence."

### **Q28. What happens if one agent fails?**

**Answer:**

"We should not treat a failed agent as a successful result. We capture the error, retry transient failures with controlled limits, and record the failure status in the workflow state.

If the agent still fails, we can route the case for manual review or mark the result as incomplete. The Fraud Analysis Agent should not interpret missing results as evidence that the applicant passed verification."

### **Q29. How do you implement human-in-the-loop?**

**Answer:**

"After the Fraud Analysis Agent generates the investigation report, we route the case to a human underwriter or fraud investigation team.

With LangGraph, we can use an interrupt to pause execution, persist the workflow state using a checkpointer, and wait for human approval.

After the human reviews the report, we resume the workflow with the human's decision.

This ensures that the AI system provides recommendations while the final decision remains with the authorized person."

## **6. GenAI and Agentic AI in your project**

### **Q30. Where exactly did you use Generative AI in your project?**

**Answer:**

"We used Generative AI through Azure OpenAI.

The main use cases were:

1. Interpreting extracted applicant information and validation findings.
2. Understanding potential document anomalies reported by forensic tools.
3. Retrieving and interpreting underwriting guidelines through RAG.
4. Correlating findings from different agents.
5. Generating structured investigation explanations and summaries.

We used LLMs where natural-language understanding and contextual interpretation were useful. We used Python and specialized tools for deterministic validation and image-forensics operations."

### **Q31. Where did you use Agentic AI?**

**Answer:**

"We implemented Agentic AI through four specialized agents orchestrated by LangGraph.

Each agent has a specific objective, its own instructions, and access to relevant tools.

The Verification Agent uses document extraction, Python, SQL, and authorized APIs. The Forgery Detection Agent uses OCR, OpenCV, and forensic tools. The Domain Knowledge Agent uses RAG and a vector database. The Fraud Analysis Agent consumes the other agents' outputs and prepares an investigation recommendation.

LangGraph manages communication, shared state, workflow execution, and routing."

### **Q32. What is the difference between Generative AI and Agentic AI?**

**Answer:**

"Generative AI generates content based on a user prompt. For example, our LLM generates explanations of income discrepancies and summaries of investigation findings.

Agentic AI goes beyond generating content. It can plan and execute a sequence of tasks, invoke tools, read results, and decide which workflow step should happen next.

In our project, Generative AI provides contextual understanding and explanations, while Agentic AI coordinates multiple specialized agents to complete the underwriting investigation workflow."

### **Q33. Are all four agents LLM-based agents?**

**Answer:**

"Not every operation inside an agent requires an LLM.

Some operations are tool-based. For example, SQL queries, Python validations, OCR extraction, and OpenCV image analysis do not need an LLM.

We use LLMs where contextual understanding, language interpretation, or explanation is needed. This hybrid design is more reliable and cost-effective than sending every operation to an LLM."

This is an especially important answer because it demonstrates that you understand the difference between an AI agent and an ordinary Python function.

## **7. Prompts for all four agents**

### **Q34. How did you write prompts for each agent?**

**Answer:**

"We designed separate system prompts for each agent based on its responsibility.

We defined the agent's role, objective, available tools, constraints, input information, and expected output format.

We also instructed the agents not to invent evidence and to return structured outputs so that the next agent could consume them."

The following are interview-ready example prompts. They demonstrate a suitable design; describe them as your exact production prompts only if they match what you actually implemented.

**1. Verification Agent – System Prompt**

```
You are an Insurance Applicant Verification Agent.

Your responsibility is to validate applicant
information using the provided extracted data,
authorized verification results, and database records.

Tasks:
1. Compare applicant names and dates of birth.
2. Compare declared income with salary documents.
3. Identify missing or inconsistent information.
4. Use only the supplied verification evidence.
5. Do not assume that a mismatch proves fraud.

Return structured JSON containing:

{
  "identity_status": "",
  "income_status": "",
  "missing_fields": [],
  "inconsistencies": [],
  "evidence": [],
  "review_required": true
}
```

**2. Forgery Detection Agent – System Prompt**

```
You are an Insurance Document Forgery Analysis Agent.

Your responsibility is to interpret document
forensics results provided by OCR, OpenCV,
metadata checks, signature validation,
and specialized forensic tools.

Tasks:
1. Review the supplied forensic findings.
2. Identify suspicious document fields.
3. Explain the evidence for possible manipulation.
4. Do not claim forgery without supporting evidence.
5. Do not invent image analysis results.
6. Recommend manual review when uncertain.

Return structured JSON containing:

{
  "document_type": "",
  "tampering_indicators": [],
  "suspicious_fields": [],
  "evidence": [],
  "review_status": "manual_review"
}
```

**3. Domain Knowledge Agent – System Prompt**

```
You are an Insurance Underwriting Domain Expert.

Your responsibility is to analyze applicant details
using the retrieved, approved underwriting guidelines.

Tasks:
1. Read the supplied guideline context.
2. Identify applicable underwriting rules.
3. Compare applicant details with those rules.
4. Explain why additional documentation is required.
5. Cite source documents and page numbers.
6. Never invent underwriting rules.
7. If the context is insufficient, say so.

Return structured JSON containing:

{
  "applicable_rules": [],
  "findings": [],
  "source_references": [],
  "review_required": true
}
```

**4. Fraud Analysis Agent – System Prompt**

```
You are a Life Insurance Fraud Analysis Agent.

Your responsibility is to analyze the findings
from the Verification, Forgery Detection,
and Domain Knowledge Agents.

Tasks:
1. Review all agent outputs.
2. Correlate independent risk indicators.
3. Separate confirmed facts from suspicions.
4. Consider the quality of supporting evidence.
5. Prioritize cases for investigation.
6. Do not invent evidence.
7. Do not automatically declare fraud.
8. Recommend human review where necessary.

Return structured JSON containing:

{
  "risk_indicators": [],
  "correlated_evidence": [],
  "investigation_priority": "",
  "explanation": "",
  "recommended_action": ""
}
```

### **Q35. What is a system prompt?**

**Answer:**

"A system prompt defines the role, behavior, responsibilities, rules, and output format of an LLM.

For example, in our project, the Verification Agent system prompt instructs the LLM to interpret verification results, identify discrepancies, avoid assumptions, and return structured JSON."

### **Q36. What is the difference between system prompt and user prompt?**

**Answer:**

"A system prompt defines how the model should behave. A user prompt provides the actual task or data for that particular request.

For example, the system prompt says the model is an insurance verification assistant. The user prompt provides the applicant details and extracted salary information that need to be analyzed."

### **Q37. How did you handle hallucinations?**

**Answer:**

"We used role-specific prompts, supplied evidence from tools, and instructed the LLM not to invent missing information.

For the Domain Knowledge Agent, we restricted responses to retrieved underwriting guidelines. For Verification and Forgery Detection, we passed structured outputs from the actual validation and forensic tools.

We also used structured output validation and human review for uncertain or high-risk cases."

### **Q38. Did you use Chain of Thought prompting?**

**Answer:**

"We used structured reasoning instructions in the prompts to guide the agents through their tasks. For example, the Fraud Analysis Agent reviews the verification, forgery, and domain findings before generating its report.

We do not depend on exposing the model's private internal reasoning. Instead, we ask it to provide a concise explanation supported by evidence."

## **8. Python, FastAPI, SQL, and backend questions**

These questions connect the project to the Python backend experience in your profile.

### **Q39. Why did you use Python?**

**Answer:**

"Python was our primary backend and AI integration language. We used it for document processing, business validations, database interactions, LLM integration, agent implementation, and workflow orchestration.

Its ecosystem provides libraries such as OpenCV, LangChain, LangGraph, FastAPI, and database connectors, which made it suitable for this project."

### **Q40. Why did you use FastAPI?**

**Answer:**

"We used FastAPI to expose backend APIs for application submission, document processing, and triggering the AI workflow.

It supports asynchronous endpoints, request validation through Pydantic, automatic API documentation, and integration with Python AI components."

Example:

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class ApplicantRequest(BaseModel):
    applicant_id: str
    name: str
    annual_income: float


@app.post("/underwriting/analyze")
async def analyze_applicant(request: ApplicantRequest):
    return {
        "applicant_id": request.applicant_id,
        "status": "analysis_started"
    }
```

### **Q41. Why do you use async and await in FastAPI?**

**Answer:**

"`async` and `await` help handle I/O-bound operations without blocking the event loop while waiting for an operation to finish.

In our application, operations such as calling an external verification API, accessing an asynchronous database client, or invoking an LLM API can involve network waiting time.

Using asynchronous functions can improve concurrency when the underlying libraries support async execution."

### **Q42. What is Pydantic?**

**Answer:**

"Pydantic is a Python data validation library. FastAPI uses it to validate request and response data.

For example, when an applicant submits income as a string instead of a number, Pydantic can validate the request and return a validation error."

### **Q43. Why did you use PostgreSQL?**

**Answer:**

"We used PostgreSQL to store and retrieve structured information such as applicant records, policy details, and investigation-related data.

The Verification Agent can query authorized records to compare submitted information with existing records."

### **Q44. How did you use SQL in verification?**

**Answer:**

"We used SQL queries to retrieve applicant and policy information using identifiers such as applicant ID or policy number.

The retrieved data is passed to Python validation logic to compare the applicant's declared details against trusted database records."

Example:

```
SELECT
    applicant_id,
    applicant_name,
    date_of_birth,
    annual_income
FROM applicants
WHERE applicant_id = 'APP1001';
```

### **Q45. What is the difference between SQL and a vector database?**

**Answer:**

"SQL databases are suitable for structured information such as applicant records, policies, and financial data.

Vector databases are designed to store embeddings and retrieve semantically similar content.

In our project, PostgreSQL handles structured applicant and policy data, while Pinecone or ChromaDB is used for retrieving relevant underwriting guideline documents."

### **Q46. How do you handle exceptions in Python?**

**Answer:**

"We use exception handling to prevent failures in one operation from crashing the entire workflow.

For example, if a document cannot be processed or an external verification API times out, we capture the exception, log the error, and return an appropriate failure status.

For transient failures, we can apply controlled retries. If the failure continues, we route the case for manual review."

Example:

```python
try:
    result = verify_applicant(applicant_data)
except TimeoutError:
    result = {
        "status": "verification_timeout",
        "review_required": True
    }
except Exception as exc:
    result = {
        "status": "processing_failed",
        "review_required": True
    }
```

### **Q47. How do you secure the application?**

**Answer:**

"We should secure the backend APIs using authentication and authorization. JWT bearer tokens can be used to authenticate API requests.

We should also validate uploaded files, restrict file sizes and types, protect sensitive applicant data, use secure connections, and ensure that agents access only authorized databases and verification services.

Sensitive information should not be unnecessarily included in LLM prompts or application logs."

## **9. Deployment, performance, and production questions**

### **Q48. How would you deploy this project into production?**

**Answer:**

"I would package the FastAPI application and its dependencies using Docker.

We can deploy the containers to an appropriate cloud environment. Azure OpenAI can provide the LLM and embedding endpoints, while PostgreSQL stores structured information and Pinecone or ChromaDB stores vector embeddings.

We should configure environment-based secrets, authentication, logging, monitoring, error handling, and appropriate scaling.

For long-running document analysis, we can use background workers or a queue so that the API does not need to hold an HTTP request open until every agent finishes."

### **Q49. How would you handle 100,000 PDF documents?**

**Answer:**

"I would design a scalable document ingestion pipeline.

First, documents would be uploaded to object storage. We would process them asynchronously in batches using workers.

The pipeline would extract text, tables, and relevant content using document intelligence tools, apply suitable chunking, generate embeddings, and store them in the vector database along with metadata such as document ID, page number, document type, and version.

I would also implement incremental indexing, duplicate detection, retry handling, and monitoring so that we don't need to reprocess all documents whenever one document changes."

### **Q50. How would you reduce LLM latency and token cost?**

**Answer:**

"I would avoid sending unnecessary document content to the LLM.

We can retrieve only the relevant chunks using RAG, reduce prompt size, use smaller models for simpler classification tasks, cache repeated retrieval results where appropriate, and use deterministic Python logic for calculations and validations.

We can also run independent agents in parallel when their tasks do not depend on one another."

### **Q51. How would you monitor the AI workflow?**

**Answer:**

"I would capture request IDs, applicant IDs using appropriate privacy controls, agent execution status, tool failures, API latency, token usage, retrieval relevance, and model errors.

I would also monitor false positives, false negatives, manual-review rates, and the quality of generated explanations.

This helps identify whether an issue is coming from document extraction, database verification, forensic detection, retrieval, or LLM generation."

### **Q52. What if the LLM generates an invalid JSON response?**

**Answer:**

"I would use structured output capabilities where available, validate the response against a Pydantic schema, and handle parsing errors.

If validation fails, we can retry with a controlled correction prompt. If the response remains invalid, the agent should return a failure status rather than passing malformed data to the next agent."

## **10. Real-time project scenario questions**

These are particularly important in a client interview because they test whether you understand the architecture beyond definitions.

### **Q53. What happens if OCR extracts incorrect information?**

**Answer:**

"First, we validate the extracted information using field-level checks and document confidence scores if provided by the OCR service.

For critical fields such as PAN, date of birth, and income, we can apply format validation and cross-document comparison.

If the extraction is uncertain, we mark the field for reprocessing or manual verification rather than allowing the LLM to assume the missing value."

### **Q54. What happens if the Domain Knowledge Agent cannot retrieve relevant guidelines?**

**Answer:**

"We should not ask the LLM to invent the missing rule. The agent returns an insufficient-context status, records the failed retrieval, and routes the case for manual underwriting review or further document retrieval."

### **Q55. What if the Verification Agent says verified but the Forgery Detection Agent flags manipulation?**

**Answer:**

"These results are not necessarily contradictory because the agents check different things.

Verification checks whether the information matches trusted records. Forgery Detection checks whether the document itself shows possible manipulation.

For example, a salary amount might match an external record, but the uploaded document may still contain suspicious editing artifacts. The Fraud Analysis Agent correlates both results and recommends further investigation."

### **Q56. What if one agent produces a false positive?**

**Answer:**

"We should not make a final decision based on a single agent's output.

The Fraud Analysis Agent considers multiple independent findings, their supporting evidence, and evidence quality. We also use calibrated detection thresholds, validation datasets, and human review to reduce the impact of false positives."

### **Q57. How would you detect duplicate documents?**

**Answer:**

"We can calculate file hashes to detect exact duplicate files. For documents that have been modified or converted, we can use perceptual hashing or image similarity techniques.

We can also compare extracted document fields and metadata to identify potential reuse across applications."

### **Q58. How would you detect income fraud?**

**Answer:**

"We extract income information from the application, salary slips, and bank statements using OCR.

Then Python compares the values, and SQL retrieves existing records where authorized. The system identifies discrepancies and passes the findings to the Fraud Analysis Agent.

If a discrepancy is significant, the case is prioritized for additional verification. A discrepancy alone does not establish fraudulent intent."

### **Q59. What happens if the applicant submits a corrupted PDF?**

**Answer:**

"We validate the uploaded file before processing. If it cannot be opened or parsed, we capture the processing error, record the document status, and request a valid document or route it for manual review.

We should not allow a corrupted document to silently pass through the verification workflow."

### **Q60. How do you decide which agent runs first?**

**Answer:**

"Applicant data and document extraction are the initial requirements. Once the necessary data is available, the Verification, Forgery Detection, and Domain Knowledge agents can run independently or in parallel.

The Fraud Analysis Agent should run only after the required outputs from those agents are available.

If a particular agent depends on another agent's result, we use graph dependencies or conditional routing in LangGraph."

## **11. Additional Agentic AI and LLM questions**

| Question                      | Interview-ready answer                                                                                             |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| What is an AI agent?          | An AI agent is a system that can use an LLM, instructions, tools, and state to perform a defined task.             |
| What is a tool in LangChain?  | A callable function or external capability that an agent can invoke, such as a database query or verification API. |
| What is a supervisor agent?   | A coordinating component that manages the workflow and delegates tasks to specialized agents.                      |
| What is a multi-agent system? | A system in which multiple specialized agents work together to complete a larger task.                             |
| What is tool calling?         | The process through which an LLM selects and invokes an available function or tool based on the task.              |
| What is RAG?                  | Retrieval-Augmented Generation retrieves relevant external information and provides it as context to an LLM.       |
| What is an LLM hallucination? | When an LLM produces information that is unsupported, incorrect, or fabricated.                                    |
| What is temperature?          | A model parameter that influences the randomness of generated responses.                                           |
| What is a context window?     | The amount of input and output tokens a model can handle in a request.                                             |
| What is structured output?    | An LLM response constrained to a defined format, such as JSON matching a schema.                                   |

## **12. Interviewer may ask: What was your individual contribution?**

This is an important question. Answer based on what you personally implemented, rather than claiming ownership of components you only studied or designed.

**Suggested answer:**

"My role was as a Software Engineer working on the backend and Generative AI integration. My responsibilities included developing Python-based backend functionality, integrating document-processing components, working with LLMs and LangChain, and implementing agent workflows using LangGraph.

I worked on integrating the different components so that the agents could process applicant information, retrieve relevant underwriting guidelines, and produce structured investigation findings."

Only include the specific responsibilities you actually performed.

## **13. Technology stack – remember this table**

| Technology                           | How it fits into the project          |
| ------------------------------------ | ------------------------------------- |
| Python                               | Backend logic and validations         |
| FastAPI                              | REST API development                  |
| LangChain                            | LLM, prompts, tools, RAG integration  |
| LangGraph                            | Agent orchestration and shared state  |
| Azure OpenAI                         | LLM inference and embeddings          |
| OCR / Azure AI Document Intelligence | Document text extraction              |
| OpenCV                               | Image preprocessing and analysis      |
| SQL / PostgreSQL                     | Applicant and policy data             |
| Pinecone / ChromaDB                  | Vector storage and semantic retrieval |
| Pydantic                             | Input and output validation           |
| Docker                               | Application containerization          |

## **14. Final 2-minute project explanation**

This is the answer I recommend practicing before your client round.

**Project introduction**

High priority

"I worked on a Life Insurance Underwriting Fraud Detection system designed to identify suspicious applicant information and potentially manipulated documents during the underwriting process.

We designed a multi-agent architecture using Python, LangChain, LangGraph, OCR, OpenCV, SQL, and Azure OpenAI.

Our system consists of four specialized agents.

First, the Verification Agent extracts applicant information using OCR and validates it against submitted documents, authorized external services, and internal databases using Python and SQL.

Second, the Forgery Detection Agent analyzes document authenticity using OCR, OpenCV, image analysis, metadata checks, and specialized forensic techniques to identify potential manipulation.

Third, the Domain Knowledge Agent uses Retrieval-Augmented Generation. We ingest approved underwriting guidelines, generate embeddings using Azure OpenAI, and store them in Pinecone or ChromaDB. When an application is analyzed, the agent retrieves relevant guidelines and uses the LLM to interpret the rules.

Fourth, the Fraud Analysis Agent receives the results from the other three agents, correlates the findings, applies deterministic Python rules, and generates an evidence-based investigation report.

We use LangGraph to orchestrate the agents and maintain shared state. LangChain integrates the LLMs, tools, and retrieval components.

Generative AI helps us interpret information, understand contextual discrepancies, and generate explanations. Agentic AI helps coordinate the specialized agents and execute the multi-step investigation workflow.

Finally, the investigation report is sent to a human underwriter or fraud investigation team. The AI provides recommendations, but the final decision remains with the human."

## **15. What you should prioritize before the interview**

### Interview Preparation Checklist

- [ ] Explain the project architecture and responsibilities of all four agents.
- [ ] Explain why LLMs are used alongside Python and OCR.
- [ ] Review LangGraph state, nodes, edges, conditional routing, and parallel execution.
- [ ] Review RAG ingestion, chunking, embeddings, vector search, and grounding.
- [ ] Review each agent's role, objectives, constraints, evidence, and JSON output.
- [ ] Practice Python concepts: dictionaries, lists, strings, decorators, generators, and exceptions.
- [ ] Review FastAPI and SQL: async/await, Pydantic, JWT, joins, indexes, and transactions.
- [ ] Review production scenarios: failures, false positives, latency, retries, and human approval.

**My most important advice:** Be ready to explain one complete example from beginning to end: an applicant declares ₹5 lakh income, the salary slip shows ₹15 lakh, the Verification Agent identifies the mismatch, the Forgery Agent checks the document for manipulation, the Domain Agent retrieves the applicable underwriting guideline, and the Fraud Analysis Agent correlates the evidence and recommends human investigation.

If you can explain that one scenario clearly, including which component does what and why the LLM is needed, you will be able to handle many follow-up questions naturally.