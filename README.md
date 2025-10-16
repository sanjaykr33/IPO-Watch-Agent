
Autonomous Investment Research Agent Workflow

Phase 1: User Request & Initial Data Collection

1. User Query Ingestion:
    The agent receives a user's request (e.g., "Analyze recent IPOs," "Find good IPOs from Q3 2024," "Compare IPOs from last month").
2. Date Range Determination:
    The agent's LLM analyzes the query to identify if a specific date range (month and year) for IPOs is provided.
    If no date range is found, the agent prompts the user to either:
        Provide a specific month and year.
        Confirm the use of "latest IPOs" (e.g., last 1-3 months).
    Once a date range is established, proceed.
3. Initial IPO Identification (Nasdaq API):
    The agent uses a dedicated tool (e.g., your Nasdaq API tool) to fetch a list of all companies that had an IPO within the determined timeframe.
    For each identified IPO, it extracts essential details like company name and IPO date.
4. Stock Symbol Extraction:
    For each company identified in the previous step, the agent uses another tool (e.g., a stock lookup API) to find its corresponding stock ticker symbol.

Phase 2: News Integration & SEC Filing Retrieval

5. Initial News & Sentiment Analysis (Around IPO Date):
    For each company/stock symbol, the agent uses a tool (e.g., your google_news_sentiment_analysis tool) to search for news articles.
    The search focuses on a period around the IPO date (e.g., 3 months before to 1 month after IPO).
    It scrapes top news articles, extracts their content, and performs sentiment analysis on each.
    The results (article URLs, titles, snippets, and sentiment scores) are stored in the database, linked to the respective company.
6. SEC Filing Strategy & Retrieval:
    For each company, the agent first checks its IPO date.
    If the IPO is relatively recent (e.g., within the last 12-18 months):
        The agent uses a tool to fetch the company's S1 filing (or F-1 for foreign issuers).
    If the IPO is older (e.g., more than a year ago):
        The agent uses tools to fetch the latest Form 10-K (Annual Report) and any subsequent Form 10-Q (Quarterly Reports).
    All fetched SEC filings are stored locally (as raw text files) and their metadata (company, filing type, date, local path) are recorded in the database.
7. Post-IPO News & Sentiment Analysis (Correlating with Financials):
    For companies where 10-K/10-Q filings were retrieved, the agent performs an additional news search and sentiment analysis.
    This search focuses on the reporting periods covered by the 10-K/10-Q filings (e.g., news during the last fiscal year or quarter).
    Results are stored in the database, linked to the company and the relevant reporting period.

Phase 3: Document Processing & Individual IPO Summarization (RAG)

8. Document Ingestion, Chunking, and Embedding:
    For each individual IPO/stock symbol:
        The agent loads all relevant documents: the S1 filing, any 10-K/10-Q reports, and the content of the news articles (both initial and post-IPO) from the local file system.
        These documents are broken down into smaller, overlapping text chunks.
        Each text chunk is converted into a numerical vector embedding using an embedding model.
        These embeddings, along with the original text chunks and rich metadata (company, filing type, date, source news URL/document path, sentiment score for news chunks), are stored in a vector database.
9. Individual IPO Summary Generation:
    For each individual IPO/stock symbol:
        The agent formulates a specific query to the vector database (e.g., "Summarize the key aspects, financials, risks, and market sentiment for [Company Name]'s IPO and subsequent performance").
        It retrieves the most semantically relevant chunks from the vector database (covering SEC filings and news).
        These retrieved chunks are provided as context to an LLM.
        The LLM then generates a comprehensive, concise summary for that specific IPO, synthesizing information from all document types and incorporating news sentiment.
        This individual summary is stored in the database.

Phase 4: Overall Evaluation & Final Output

10. Aggregated IPO Evaluation:
    The agent collects all the individual IPO summaries generated in the previous step.
    It also retrieves the overall news sentiment trends for each company (e.g., "overall positive," "mixed," "initial positive, later negative").
    All this aggregated information is then provided as context to an LLM.
    The LLM analyzes this consolidated view to identify patterns, strengths, and weaknesses across the IPOs.
    Based on predefined criteria (or its own learned understanding), the LLM determines which IPOs are "good" and which are "not good" (or provides a more nuanced ranking/categorization).
11. Final Output to User:
    The agent presents the user with the overall analysis, including the categorization of IPOs, key reasons for the assessment, and potentially links to the individual summaries or original documents.
