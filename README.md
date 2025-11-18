# Self-Correcting RAG Agent using LangGraph & DeepSeek-Coder

<div style="text-align: center;">
  <img src="https://miro.medium.com/v2/resize:fit:1400/1*vwNcz08f6a0bUZWyuLqGDA.png" style="width:600px;" alt="شرح Self-Correcting RAG Agent" />
</div>



An advanced **Retrieval-Augmented Generation (RAG)** system featuring an intelligent **self-correcting pipeline** powered by **LangGraph** and **DeepSeek-Coder**.  
This agent detects poor retrieval results, automatically **rewrites search queries**, and **retries** until it finds the most relevant context.

---

## ✨ Key Features

###  Self-Correction Loop
- Evaluates retrieved documents using an **LLM grading node**.
- If relevance is low, it loops back to **rewrite the query** and retries retrieval.

###  Smart Query Rewriting
- Uses an LLM to transform vague user input into a **concise, targeted search query**.

###  Stateful Flow with Memory
- Built using **LangGraph** and **GraphState** to store and track `question_history`.
- Prevents infinite loops and improves reasoning consistency.

###  Efficient LLM Inference
- Runs the **DeepSeek-Coder 1.3B** model
- **4-bit quantization (BitsAndBytes)** for fast inference even on limited GPU resources

---

## System Architecture

The workflow is implemented as a **State Graph** with four main nodes:

| Node | Role | Action | Routing |
|------|------|---------|-----------|
| `retrieve` | Search | Fetches top-k docs from FAISS Vector Store | → `grade_documents` |
| `grade_documents` | Quality Check | LLM evaluates relevance | Relevant → `generate`<br>Not Relevant → `rewrite` |
| `rewrite` | Query Refinement | Rewrites query for better retrieval | → `retrieve` *(Self-Correction Loop)* |
| `generate` | Answer | Generates final answer using relevant context | → **END** |

---

##  Workflow Graph Overview

```mermaid
graph TD
    A[User Question] --> B[Retrieve]
    B --> C[Grade Documents]

    C -- Relevant --> D[Generate Answer]
    C -- Not Relevant --> E[Rewrite Query]
    E --> B

    D --> F[END]
