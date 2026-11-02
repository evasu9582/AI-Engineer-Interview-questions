19. What is summarization, and how will you implement it?

Summarization is the process of converting long text or documents into a shorter version while keeping the important information and main meaning.

Interview-ready answer:

Summarization is the process of reducing long documents into shorter, meaningful content while preserving key information. I would implement it by extracting text from documents, splitting large documents into smaller chunks, and passing each chunk to an LLM to generate summaries. Finally, I would combine the chunk summaries to create one overall summary of the document.

Two common types:

1. Extractive summarization: Selects important sentences directly from the original text.
2. Abstractive summarization: Generates new, shorter sentences using an LLM while preserving the original meaning.

20. What is scalability, and how do you make your GenAI application scalable?

Scalability means the ability of an application to handle increasing numbers of users, requests, and data without affecting performance.

Interview-ready answer:

Scalability means an application can handle increasing users, requests and data efficiently. I would make my GenAI application scalable by using cloud infrastructure, load balancing, parallel processing, batch processing and asynchronous tasks. I would also use caching, scalable vector databases and auto-scaling to handle increased workloads while maintaining performance.

Easy way to remember: Cloud → Load Balancing → Parallel Processing → Caching → Auto-scaling.


21. What is prompting, and what are the different prompting techniques?

Prompting is the process of giving instructions or questions to an LLM to get the desired response.

Interview-ready answer:

Prompting is the process of providing clear instructions, context and examples to an LLM to get accurate and relevant responses. Different prompting techniques include Zero-shot prompting, One-shot prompting, Few-shot prompting, Chain-of-Thought prompting, Role-based prompting and Contextual prompting. The choice of technique depends on the task and the expected output.

### Common prompting techniques

1. Zero-shot prompting: Asking a question without providing examples.
2. One-shot prompting: Providing one example before asking the question.
3. Few-shot prompting: Providing multiple examples to guide the model.
4. Chain-of-Thought prompting: Encouraging the model to break a problem into steps.
5. Role-based prompting: Assigning a role, such as "Act as an AI Engineer."
6. Contextual prompting: Providing relevant background information to get a more accurate response.

Easy way to remember: Zero-shot → One-shot → Few-shot → Chain-of-Thought → Role-based → Contextual.

22. Why do we need guardrails in an LLM, and how do you implement them?

ans).

    Interview-ready answer:

    We need guardrails to make LLM applications safe, secure and reliable. They help prevent harmful responses, prompt injection, sensitive data leakage and irrelevant answers. I would implement guardrails using input validation, prompt injection detection, content filtering, PII detection, output validation and access control. This ensures the LLM follows the application's rules and provides safe responses.

    ### Common guardrail techniques

    1. Input validation: Block invalid or malicious inputs.
    2. Prompt injection protection: Prevent manipulation of the LLM.
    3. Content filtering: Block harmful or inappropriate content.
    4. PII detection: Protect sensitive information.
    5. Output validation: Check responses before showing them to users.
    6. Access control: Restrict access to authorized documents and tools.

    Popular tools: NVIDIA NeMo Guardrails, Guardrails AI and Llama Guard.

    Easy way to remember: Input Validation → Prompt Protection → Content Filtering → PII Detection → Output Validation.

23. What is fine-tuning?

ans). Fine-tuning is the process of taking an already pre-trained LLM and training it further using specific domain-related data to improve its performance for a particular task.

    Interview-ready answer:

    Fine-tuning is the process of customizing a pre-trained LLM using domain-specific data to improve its performance for a particular task. For example, we can fine-tune an existing LLM using company-related question-answering data so that it understands domain-specific terminology and provides better responses.

    ### Example

    If we have a general LLM and want it to answer banking-related questions more effectively, we can fine-tune it using banking-related datasets.

    ### Fine-tuning vs RAG

    - Fine-tuning: Teaches the model specific patterns, behavior and terminology by updating its weights.
    - RAG: Provides relevant external information to the model without changing its weights.

    Easy way to remember: Fine-tuning = Teach the model. RAG = Give the model information.

24. What are the initial steps in an Agentic AI flow?

ans). An Agentic AI flow starts by understanding the user's goal, planning the required tasks and deciding which tools to use.

    Interview-ready answer:

    The initial steps in an Agentic AI flow are understanding the user's request, identifying the goal, breaking it into smaller tasks, planning the execution steps and selecting the required tools. Then, the agent executes the tasks, observes the results and decides the next action until the goal is completed.

    ### Main steps in Agentic AI

    1. User Input: Receive the user's request.
    2. Goal Understanding: Understand what the user wants.
    3. Planning: Break the goal into smaller tasks.
    4. Tool Selection: Decide which tools or APIs are needed.
    5. Execution: Execute the planned tasks.
    6. Observation: Check the results.
    7. Decision: Decide whether to continue, retry or finish.
    8. Final Response: Return the result to the user.

    Easy way to remember: Understand → Plan → Select Tools → Execute → Observe → Decide → Respond.

25. what is fine tuning?

ans). Fine-tuning is the process of taking an already pre-trained LLM and training it further using specific domain-related data to improve its performance for a particular task.

Interview-ready answer:

Fine-tuning is the process of customizing a pre-trained LLM using domain-specific data to improve its performance for a particular task. For example, we can fine-tune an existing LLM using company-related question-answering data so that it understands domain-specific terminology and provides better responses.

### Example

If we have a general LLM and want it to answer banking-related questions more effectively, we can fine-tune it using banking-related datasets.

### Fine-tuning vs RAG

- Fine-tuning: Teaches the model specific patterns, behavior and terminology by updating its weights.
- RAG: Provides relevant external information to the model without changing its weights.

Easy way to remember: Fine-tuning = Teach the model. RAG = Give the model information.

26. What are the initial steps in an Agentic AI flow?

An Agentic AI flow starts by understanding the user's goal, planning the required tasks and deciding which tools to use.

Interview-ready answer:

The initial steps in an Agentic AI flow are understanding the user's request, identifying the goal, breaking it into smaller tasks, planning the execution steps and selecting the required tools. Then, the agent executes the tasks, observes the results and decides the next action until the goal is completed.

### Main steps in Agentic AI

1. User Input: Receive the user's request.
2. Goal Understanding: Understand what the user wants.
3. Planning: Break the goal into smaller tasks.
4. Tool Selection: Decide which tools or APIs are needed.
5. Execution: Execute the planned tasks.
6. Observation: Check the results.
7. Decision: Decide whether to continue, retry or finish.
8. Final Response: Return the result to the user.

Easy way to remember: Understand → Plan → Select Tools → Execute → Observe → Decide → Respond.

27. What are State, Tools and Conditions in Agentic AI?

In Agentic AI, State, Tools and Conditions help an agent manage information, perform tasks and decide what to do next.

### 1. State

State is the information or memory maintained by the agent during workflow execution.

Example: User query, conversation history, retrieved documents and previous tool results.

### 2. Tools

Tools are external functions or APIs that an agent uses to perform tasks.

Example: Search engines, databases, Python functions, calculators and APIs.

### 3. Conditions

Conditions are decision-making rules that determine the next step based on the current state or tool results.

Example: If relevant documents are found, send them to the LLM. Otherwise, perform another search.

### Interview-ready answer

In Agentic AI, State stores the information and results throughout the workflow. Tools allow the agent to perform external tasks such as searching databases, calling APIs and executing functions. Conditions help the agent make decisions and choose the next step based on the current state or execution results. These concepts are commonly used in LangGraph to build intelligent workflows.

Easy way to remember:

- State = What the agent knows.
- Tools = What the agent can do.
- Conditions = What the agent should do next.