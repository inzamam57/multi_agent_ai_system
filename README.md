# ResearchMind — Multi-Agent AI Research Pipeline

A 4-agent research pipeline built with LangChain + LangGraph, powered by **Mistral** (LLM) and **Tavily** (web search), with a Streamlit front end.

**Pipeline:**
1. **Search Agent** — finds recent, relevant sources on your topic (Tavily)
2. **Reader Agent** — scrapes the most relevant URL for deeper content
3. **Writer Chain** — drafts a structured research report
4. **Critic Chain** — reviews and scores the report

---

## 1. Requirements

- Python 3.10+
- A free [Mistral API key](https://console.mistral.ai/)
- A free [Tavily API key](https://app.tavily.com/)

---

## 2. Setup — step by step

### Step 1: Get your project folder ready
Unzip the project and open a terminal inside that folder.

### Step 2: Create a virtual environment (recommended)
```bash
python -m venv venv
source venv/bin/activate      # macOS/Linux
venv\Scripts\activate         # Windows
```

### Step 3: Install dependencies
```bash
pip install -r requirements.txt
```

### Step 4: Create your `.env` file
Copy the example file and fill in your real keys:
```bash
cp .env.example .env          # macOS/Linux
copy .env.example .env        # Windows
```

Then open `.env` in a text editor and paste in your actual keys:
```env
MISTRAL_API_KEY=your_real_mistral_key
TAVILY_API_KEY=your_real_tavily_key
```

**Where to get each key:**
| Key | Where to get it | Free tier? |
|---|---|---|
| `MISTRAL_API_KEY` | https://console.mistral.ai/ → API Keys | Yes |
| `TAVILY_API_KEY` | https://app.tavily.com/ → Overview → API Keys | Yes (1,000 searches/mo) |

⚠️ Never commit `.env` to git — it contains secrets. Only `.env.example` (no real keys) should be committed.

### Step 5: Run the app

**Option A — Streamlit UI (recommended):**
```bash
streamlit run app.py
```
This opens a browser tab where you type a topic and click "Run Research Pipeline."

**Option B — Command line:**
```bash
python pipeline.py
```
This will prompt you to type a topic directly in the terminal.

---

## 3. Project structure

```
.
├── agents.py          # LLM setup (Mistral) + agent/chain definitions
├── tools.py           # web_search (Tavily) and scrape_url tools
├── pipeline.py         # CLI runner — orchestrates the 4-step pipeline
├── app.py             # Streamlit UI
├── requirements.txt   # Python dependencies
├── .env.example       # Template for your API keys (copy to .env)
└── README.md
```

---

## 4. Troubleshooting

- **`httpx.HTTPStatusError: 403 ... "tier_not_allowed"`** → your Mistral account's tier doesn't have access to the model set in `agents.py`. This project defaults to `mistral-small-latest`, which works on Mistral's free tier. If you still hit this error, log into [console.mistral.ai](https://console.mistral.ai/) → check which models your workspace/tier allows, and update the `model=` value in `agents.py` accordingly.
- **`MistralAPIException: Unauthorized` / 401** → check `MISTRAL_API_KEY` is set correctly in `.env` and has no extra spaces/quotes.
- **Empty/failed search results** → check `TAVILY_API_KEY` and your Tavily usage quota.
- **`ModuleNotFoundError`** → re-run `pip install -r requirements.txt` inside your activated virtual environment.
- **Agent doesn't call tools properly** → make sure you're using a tool-calling-capable Mistral model. `mistral-small-latest` supports tool calls; some very small/legacy models may not.
- **Works in `python pipeline.py` but not `streamlit run app.py`, or vice versa** → they're two separate ways to run the same pipeline; run only one at a time per terminal, and check the Streamlit page's red error box (not just the terminal) for the actual traceback.

---

## 5. Customizing

- Change the model in `agents.py`: swap `"mistral-large-latest"` for `"mistral-small-latest"` or `"open-mistral-nemo"` for lower cost.
- Adjust `max_results` in `tools.py`'s `web_search` to fetch more/fewer sources.
- Edit the prompts in `agents.py` (`writer_prompt`, `critic_prompt`) to change report style or scoring format.
