# EU Data Labs — Zero-Browser API Scrapers & MCP Tools for AI Agents

Production-ready, zero-browser, pay-per-event data extractors hosted on [Apify](https://apify.com/eu-data-labs) and callable natively via **Model Context Protocol (MCP)**, REST API, Python, or Node.js.

Built for quant researchers, biotech/compliance analysts, European procurement teams, and autonomous AI agents that need **clean structured JSON** without managing proxies, headless browsers, or brittle HTML parsers.

## Available Tools (Live on Apify Store & MCP)

| Tool | Source | What It Extracts | Direct Apify URL | Pay-Per-Event |
|---|---|---|---|---|
| **EU TED Public Tenders** | Official EU TED API v3 | Active EU government procurement notices, CPV codes, countries, lot descriptions, deadlines & values | [eu-data-labs/ted-europa-tenders-scraper](https://apify.com/eu-data-labs/ted-europa-tenders-scraper) | `$0.005` start + `$0.003`/notice |
| **SEC EDGAR Form 4 Insiders** | Official SEC EDGAR XML | Corporate insider buys/sells, executive titles, share counts, transaction prices & USD values | [eu-data-labs/sec-edgar-insider-trading-scraper](https://apify.com/eu-data-labs/sec-edgar-insider-trading-scraper) | `$0.005` start + `$0.003`/filing |
| **ClinicalTrials.gov v2 Pipeline** | Official ClinicalTrials API v2 | NCT study IDs, trial phases, sponsors, enrollment targets, primary endpoints, sites & contacts | [eu-data-labs/clinicaltrials-gov-scraper](https://apify.com/eu-data-labs/clinicaltrials-gov-scraper) | `$0.005` start + `$0.0025`/study |
| **Polymarket & Kalshi Odds** | Polymarket Gamma + Kalshi v2 | Normalized YES/NO probabilities, bid/ask spreads, 24h volume & liquidity across both venues | [eu-data-labs/polymarket-kalshi-odds-scraper](https://apify.com/eu-data-labs/polymarket-kalshi-odds-scraper) | `$0.005` start + `$0.002`/market |
| **Hyperliquid Perp Funding & OI** | Hyperliquid Info API | Live hourly & annualized (APR) funding rates, open interest USD, 24h volume, mark vs oracle spread across 230+ perps | [eu-data-labs/hyperliquid-perp-funding-oi-scraper](https://apify.com/eu-data-labs/hyperliquid-perp-funding-oi-scraper) | `$0.005` start + `$0.002`/pair |
| **USASpending Federal Contracts** | Official USASpending.gov API v2 | U.S. federal contract awards, recipients, amounts, awarding agencies, performance dates, and official award links | [eu-data-labs/usaspending-federal-contracts-scraper](https://apify.com/eu-data-labs/usaspending-federal-contracts-scraper) | `$0.005` start + `$0.0005`/record |

---

## Use via Model Context Protocol (MCP)

Connect all six data feeds directly to **Claude Desktop**, **Cursor**, **Windsurf**, or any MCP-compatible agent via Apify's hosted MCP server:

```json
{
  "mcpServers": {
    "eu-data-labs": {
      "url": "https://mcp.apify.com/?actors=eu-data-labs/ted-europa-tenders-scraper,eu-data-labs/sec-edgar-insider-trading-scraper,eu-data-labs/clinicaltrials-gov-scraper,eu-data-labs/polymarket-kalshi-odds-scraper,eu-data-labs/hyperliquid-perp-funding-oi-scraper,eu-data-labs/usaspending-federal-contracts-scraper",
      "headers": {
        "Authorization": "Bearer YOUR_APIFY_API_TOKEN"
      }
    }
  }
}
```

---

## Quickstart (Python)

```bash
pip install apify-client
```

### 1. Screen Top Hyperliquid Funding Rate Opportunities
```python
from apify_client import ApifyClient

client = ApifyClient("YOUR_APIFY_TOKEN")
run = client.actor("eu-data-labs/hyperliquid-perp-funding-oi-scraper").call(run_input={
    "maxItems": 20,
    "sortBy": "absFundingApr",
})
for row in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(row["coin"], row["fundingAprPct"], row["openInterestUsd"])
```

### 2. Fetch Normalized Polymarket + Kalshi Prediction Odds
```python
run = client.actor("eu-data-labs/polymarket-kalshi-odds-scraper").call(run_input={
    "venues": ["polymarket", "kalshi"],
    "maxItems": 25,
})
for m in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(m["venue"], m["question"], m["yesProbability"], m["volume24hUsd"])
```

### 3. Monitor EU Government Tenders (Official TED v3 API)
```python
run = client.actor("eu-data-labs/ted-europa-tenders-scraper").call(run_input={
    "expertQuery": "notice-type=cn-standard SORT BY publication-date DESC",
    "onlyActiveScope": True,
    "maxItems": 25,
})
for tender in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(tender.get("publicationNumber"), tender.get("title"))
```

### 4. Track SEC Form 4 Insider Transactions
```python
run = client.actor("eu-data-labs/sec-edgar-insider-trading-scraper").call(run_input={
    "maxItems": 20,
})
for filing in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(filing.get("issuerTicker"), filing.get("reportingOwner"), filing.get("totalValueUsd"))
```

### 5. Export ClinicalTrials.gov Phase 3 Biotech Pipelines
```python
run = client.actor("eu-data-labs/clinicaltrials-gov-scraper").call(run_input={
    "query": "oncology",
    "maxItems": 20,
})
for study in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(study.get("nctId"), study.get("briefTitle"), study.get("overallStatus"))
```

---

## Why Zero-Browser API Extractors?

- **Sub-5 second cold runs**: Direct official JSON/XML endpoints instead of Chromium overhead.
- **Deterministic schemas**: Strict error handling (upstream HTTP failures fail loudly instead of returning silent empty datasets).
- **Predictable pay-per-event pricing**: No monthly subscription lock-in; pay only for records actually returned.

## License

MIT — see [LICENSE](LICENSE).

### Newly Added AI & Startup Intelligence Actors (Zero-Browser, MCP-Ready)
| Actor | Store Link | Data Source |
| --- | --- | --- |
| **HuggingFace Trending AI Models, Daily Papers & Datasets** | [`eu-data-labs/huggingface-ai-models-papers-scraper`](https://apify.com/eu-data-labs/huggingface-ai-models-papers-scraper) | Official HuggingFace Hub API (`models`, `daily_papers`, `datasets`) |
| **HackerNews & YC Show HN, Jobs & Tech Trends** | [`eu-data-labs/hackernews-yc-leads-trends-scraper`](https://apify.com/eu-data-labs/hackernews-yc-leads-trends-scraper) | Official Algolia HN Search API (`show_hn`, `front_page`, `job`, `ask_hn`) |
| **DeFiLlama DeFi Yields, APY & Protocol TVL** | [`eu-data-labs/defillama-tvl-yields-stablecoins-scraper`](https://apify.com/eu-data-labs/defillama-tvl-yields-stablecoins-scraper) | Official DeFiLlama API (14,900+ pools & 9,000+ protocols) |
| **GitHub Trending & Fast-Rising Repositories** | [`eu-data-labs/github-trending-repos-leads-scraper`](https://apify.com/eu-data-labs/github-trending-repos-leads-scraper) | Official GitHub REST API (star velocity, topics & deal-flow) |
| **DexScreener Trending Tokens & Live DEX Pairs** | [`eu-data-labs/dexscreener-trending-pairs-scraper`](https://apify.com/eu-data-labs/dexscreener-trending-pairs-scraper) | Official DexScreener API (boosts, liquidity, FDV, 24h DEX volume) |

## High-Demand B2B Lead Generation & E-Commerce Actors (Zero-Browser, Pay-Per-Event)

| Actor | Best For | Pricing | Link |
|---|---|---|---|
| **Website Email, Phone & Social Contact Details Extractor** | Bulk B2B email enrichment, phone/LinkedIn/X/IG/GitHub extraction & tech stack detection | `$0.005/start + $0.001/domain` | [Run on Apify](https://apify.com/eu-data-labs/website-email-contact-extractor) |
| **Shopify Store Products, Pricing, Stock & B2B Leads Scraper** | Full `/products.json` catalog, variants, SKUs, discounts, stock + store business emails & socials | `$0.005/start + $0.0005/record` | [Run on Apify](https://apify.com/eu-data-labs/shopify-store-products-leads-scraper) |
| **Reddit Fast Search, Subreddit Posts & B2B Lead Monitor** | Keyword buyer-intent monitoring, competitor mentions & SaaS lead discovery across Reddit | `$0.005/start + $0.0005/post` | [Run on Apify](https://apify.com/eu-data-labs/reddit-fast-search-lead-monitor) |
| **YouTube Channel Business Email, Stats & Social Leads Scraper** | Influencer & creator sponsorship outreach (emails + linked-site enrichment + socials) | `$0.005/start + $0.001/channel` | [Run on Apify](https://apify.com/eu-data-labs/youtube-channel-email-leads-scraper) |
