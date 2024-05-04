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

*** Detailed Pipeline (including rationales, alternatives)***

1. - Overall Stack = Langchain, OpenAI, Gradio
   - Langchain: Easy modules to use Chat and Embedding and integrate with other functionality e.g. RAG, Pydantic, etc
   - OpenAI: Easy setup, easy to use with Langchain
   - Gradio: Almost a no-code solution and perfect as I only care about simple I/O and don't want to worry about frontend

2. - Data collection and cleaning = sec_edgar_downloader, regex, BeautifulSoup4
   - sec_edgar_downloader: Easy to download 10-K Filings automatically by just giving a valid company Ticker
   - regex: Basic pattern to naively extract the years mentioned in the input prompt
   - BeautifulSoup4 (bs4): The 10-K filing is an HTML part of the downloaded XML (from sec_edgar_downloader) so bs4 is the easiest way to extract the actual textual content and remove all HTMLl fluff

3. - Text Embedding: RecursiveCharacterTextSplitter, ChromaDB, RAG
   - RecursiveCharacterTextSplitter: HTML content is not consistent with spaces, exit characters, newlines etc so a CharacterTextSplitter would have been inefficient and lead to inconsistent chunks. Also, we are only dealing with the raw text from the HTML, not an HTML itself (would have been very very large docs so very expensive to embed) so we can't use the HTMLTextSplitter. Thus, for the sake of simplicity and chunk size consistency we use Recursive to embed docs of ~200 tokens each
   - ChromaDB: Very basic vector database for local storage. I don't need anything fancy or cloud-based as my embeddings are small (relatively), I'm storing them locally and I want fast retrieval. Also easy to use with Langchain.
   - RAG: Performed by cosine similarity search of embedded documents to retrieve 15/(num_years_mentioned) docs per mentioned year so that a total of 3000 tokens are filled up by context.

4. - Prompting: SystemPrompt, HumanPrompt, Pydantic, PydanticOutputParser
   - SystemPrompt: Short prompt to make sure GPT goes in the right direction
   - HumanPrompt: Long prompt with question (default/user input) + context + output format instructions.
   - Pydantic: GPT outputs have to be plotted automatically and they can vary in number of datapoints/years, number of total metrics/plots, etc so we create a nested Pydantic object. First we create a Pydantic object for each Insight (plot), and then another object that contains a list of Insights and a summary. Pydantic allows easy creation of custom objects that can be understood by GPT via Field descriptions.
   - PydanticOutputParser: A langchain module that allows me to feed in my custom Pydantic class (even if it uses other Pydantic objects within it) and it can generate output instructions to feed into GPT (as mentioned above). It also parses the GPT output json to make into my Pydantic object.

5. - Plotting: Matplotlib
   - Matplotlib: Easiest way to automatically make plots from my Insight objects. Also with Matplotlib I can combine multiple plots into a single figure, and that figure can be directly fed into Gradio to display.

6. - Note: code contains basic docstrings and comments for all modules
   - Attached: Image/Video of app usage


