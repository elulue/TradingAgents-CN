This is guidance for Claude Code (https://claude.ai/artifacts/code) on working with this repository.

## Overview

TradingAgents-CN is a multi-agent LLM framework for Chinese financial trading, forked from TauricResearch/TradingAgents with automated upstream sync. Version 0.1.10 features real-time progress tracking and intelligent session management.

## Commands

### Docker Commands
```bash
# Build and start all services
docker-compose up -d

# Start with management tools (Mongo Express)
docker-compose --profile management up -d

# View logs
docker-compose logs -f web

# Stop all services
docker-compose down
```

### Development Commands
```bash
# Install dependencies
pip install -r requirements.txt

# Run Streamlit web interface (local)
streamlit run web/app.py

# CLI entry point (after installation)
tradingagents
```

### Testing
No formal test runner configuration found despite 285 test files in tests/ directory. Run individual test files directly:
```bash
python -m pytest tests/test_specific_file.py
```

### Linting and Type Checking
No lint or typecheck configurations found. When implementing code changes, manually verify:
- Python code follows PEP 8 conventions
- Type hints are used where appropriate
- No unused imports or variables

## Architecture

### Multi-Agent System
The framework implements collaborative financial analysis through specialized agents:
- **Market Analyst**: Technical analysis and market trends
- **Fundamental Analyst**: Company financials and valuation
- **News Analyst**: News sentiment and event impact
- **Social Sentiment Analyst**: Social media and public sentiment
- **Bullish/Bearish Researchers**: Opposing viewpoints for balanced analysis
- **Trader**: Execution strategies and position management
- **Risk Manager**: Portfolio risk assessment

Agents collaborate using LangChain/LangGraph StateGraph for workflow orchestration.

### Service Architecture (Docker)
- **Web Service** (port 8501): Streamlit UI with Xvfb for PDF generation
- **MongoDB**: Token usage tracking and session persistence
- **Redis**: Caching and session management with fallback to file-based storage
- **Redis Commander** (port 8081): Redis management UI
- **Mongo Express** (port 8082): MongoDB management UI (optional profile)

### Data Sources
**Chinese Markets**:
- Tushare: A-share market data (requires token)
- AkShare: Free financial data API
- BaoStock: Historical A-share data

**International Markets**:
- yfinance: Yahoo Finance data
- finnhub: Real-time market data (requires API key)
- EODHD: End-of-day historical data

### Key Components

**Session Management** (v0.1.10):
- Hybrid storage: Redis (preferred) with file-based fallback
- Browser fingerprinting for stable session identification
- Automatic session recovery across page refreshes

**Progress Tracking** (v0.1.10):
- Real-time AsyncProgressTracker
- Step-by-step agent execution visibility
- Error handling with detailed feedback

**LLM Providers**:
- OpenAI (GPT models)
- Anthropic (Claude)
- Google AI (Gemini)
- Alibaba DashScope (Qwen)
- DeepSeek

**Environment Configuration**:
- Primary: `.env` file with comprehensive settings
- Docker overrides: MongoDB and Redis URLs in docker-compose.yml
- Timezone: Asia/Shanghai (configurable)

### Upstream Sync Strategy
Automated GitHub Actions workflow (`.github/workflows/upstream-sync-check.yml`):
- Weekly checks for TauricResearch/TradingAgents updates
- Automated issue creation with priority analysis
- Auto-sync for low-risk updates
- Manual sync script: `python scripts/sync_upstream.py`

### Version Inconsistency
Note: setup.py shows version 0.1.0 while pyproject.toml shows 0.1.9 and README references 0.1.10. Verify actual version before releases.

## Important Directories
- `web/`: Streamlit application code
- `tradingagents/`: Core framework implementation
- `tests/`: 285 test files (no runner configured)
- `scripts/`: Utility scripts including upstream sync
- `logs/`: Application logs (Docker volume mapped)
- `docs/`: 50,000+ words of Chinese documentation

## Dependencies
37 packages including:
- Core: langchain, langgraph, pandas, numpy
- Chinese market: tushare, akshare, baostock
- International: yfinance, finnhub-python
- UI: streamlit, plotly, chainlit
- Storage: pymongo, redis
- Utilities: rich (CLI), questionary (prompts), pypandoc (exports)

## Notes
- No AI assistant configuration files (.cursor/rules, .cursorrules, .github/copilot-instructions.md) found
- ChromaDB included for vector storage capabilities
- PDF generation requires Xvfb in Docker (handled by start-xvfb.sh)
- MongoDB authentication required in Docker deployment