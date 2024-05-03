# Company Insight Generator using LLM Inference on SEC 10-K Filings by Keshav Nath
As part of my application to GaTech Fintech Lab

**Summarized pipeline:**

1. Enter ticker and insight
2. Download all available yearly SEC 10-K Filings of that ticker
3. Select relevant years and clean up filing data
4. Break data into chunks and save embeddings to disk
5. RAG - Take relevant chunks of embedded data and feed them to GPT alongside prompt and output instructions
6. Get output as a json full of insights (including data for plots)
7. Create plots from insights and print a short text summary of the findings

***File information:***
- GaTech_Asgn_Gradio = Fully functionized solution with basic comments and docstrings, with a demo hosted via Gradio (basic I/O)
- GaTech_Asgn_Raw = First version of proposed solution, no OOPS/Functional programming, used as base for the final version


