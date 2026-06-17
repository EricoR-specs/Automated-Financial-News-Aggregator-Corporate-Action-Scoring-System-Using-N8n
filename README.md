# Automated-Financial-News-Aggregator-Corporate-Action-Scoring-System-Using-N8n
Note: This architecture and data pipeline are fully modeled after the workflow configuration file **Berita Harian Rico.json**.

#### **Problem Statement**

For stock market investors and analysts tracking the Indonesian Stock Exchange (IDX), staying updated on daily news is crucial. However, the immense volume of daily financial articles creates a high level of "noise" (e.g., repetitive macroeconomic updates and daily index fluctuations), which frequently buries high-impact "signals" (e.g., specific corporate actions that serve as price catalysts). Manually filtering these sources is time-consuming, inefficient, and prone to oversight.

#### **Solution**

An automated, self-hosted data pipeline engineered on a private VPS using **n8n**. The system ingests live financial news from multiple authoritative Indonesian media outlets, executes automated data deduplication, and utilizes a custom heuristic rule-based JavaScript engine to prioritize and score critical corporate actions—such as Acquisitions, Rights Issues, Private Placements, and Material Transactions—over generic market commentary.

```
[Schedule Trigger (00:00 WIB)] 
               │
               ▼
[Multi-Source RSS Ingestion] (Google News, CNBC, Detik)
               │
               ▼
[Merge RSS Node] 
               │
               ▼
[Filter & Score Berita (Custom JS Engine)] ───► Eliminates cross-media duplicates
               │                           ► Scores Corporate Actions (0.5 - 3.0)
               ▼
[IF Ada Berita (Conditional Routing)]
               │
               ▼
[Kirim ke Telegram (Telegram Bot API)] ─────► Dispatches clean Markdown Top 10 digest

```

#### **Key Features & System Architecture**

* **Automated Cron Scheduling:** Orchestrated via an n8n Schedule Trigger that fires daily at 00:00 WIB (17:00 UTC) to aggregate and analyze all end-of-day market news cycle updates.


* **Multi-Channel Data Aggregation:** Connects directly to real-time RSS feeds from Google News, CNBC Indonesia Market, CNBC Indonesia Finance, and Detik Finance.


* **Data Sanitization & Deduplication:** Consolidates all four incoming data streams through an n8n Merge node and processes them via JavaScript using a `Set` data structure to identify and eliminate cross-published duplicate URLs.


* **Heuristic Text-Scoring Engine:** Evaluates news headlines programmatically based on a custom categorical priority matrix:


* *Score 3.0:* High-impact catalysts (Acquisitions, Takeovers, Ownership Changes, Asset Inbreng).


* *Score 2.0:* Structural capital events (Rights Issues, Private Placements, Material Transactions).


* *Score 1.0:* Affiliated transactions.


* *Score 0.5:* General macro/IHSG commentary (treated strictly as a fallback layer when no primary catalysts are present).




* **Automated Alerting Pipeline:** Validates payload content via an conditional node, sorts the data by highest priority score, caps the output to the Top 10 most critical articles, and dispatches a clean, Markdown-formatted digest directly to a Telegram channel via the Telegram Bot API.



#### **Tech Stack**

* **Workflow Automation & Orchestration:** n8n (Self-hosted on VPS)


* **Core Processing Engine:** Node.js / JavaScript (Data transformations and heuristic filtering)


* **Delivery Infrastructure:** Telegram Bot API
