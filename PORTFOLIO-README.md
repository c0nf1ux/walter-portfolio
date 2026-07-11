# Walter — Multi-Strategy Autonomous Trading System

> A production-grade autonomous trading agent with 4 independent strategies, civic accountability data scraping, and publisher system for signal distribution.

**Status:** Production-ready | **Active Agents:** 4 (equity, crypto, biomimicry, publisher) | **Root Cause Fixes:** 4 critical bugs | **Data Scrapers:** 6 active

## The Vision

Walter automates trading using multiple independent strategies:
- Equity trading (RSI + VWAP mean reversion)
- Cryptocurrency trading (same strategy, tuned for volatility)
- Biomimicry/emerging tech catalyst trading (arXiv + bioRxiv research tracking)
- Publisher system (signal aggregation, content generation, human approval)

Plus: Civic accountability data scraping (6 independent data sources) for investigative capability.

**Why It Matters:** Production trading requires systematic debugging, parameter tuning, and risk management. Walter demonstrates all three at scale.

## Core Features

### 4 Trading Agents

**1. Equity Agent (main.py)**
- RSI + VWAP mean reversion strategy
- Daily rebalancing with watermark tracking
- Stop-loss at 8% with per-symbol 45-min cooldown
- Self-tuning parameters (RSI thresholds, confidence scoring)

**2. Crypto Agent (main_crypto.py)**
- Same RSI + VWAP strategy, tuned for volatile crypto
- Per-symbol cooldown tracking (BTCUSD/ETHUSD keyed for Alpaca format)
- Parameter evolution: confidence_threshold 0.59→0.65, rsi_oversold 42→45
- Reduced frequency after analysis (48 trades in 9 days → fewer low-conviction trades)

**3. Biomimicry/Emerging Tech Agent (main_bio.py)**
- Catalyst trading on scientific breakthroughs
- Topic-phrase matching: 70+ phrases matched to 9 small-cap tickers (TWST, EDIT, BEAM, RGTI, QBTS, etc.)
- Requires ≥2 phrase hits per signal (avoids false positives)
- Entity matching prioritized over topic matching

**4. Publisher Agent (walter-publisher)**
- Signal aggregator: Fuses signals from all 3 trading agents
- Conviction scorer: Accumulates historical signals over 30-day window
- Content generator: Creates newsletter briefs of approved drafts
- Dashboard: Human approval workflow before publishing
- API: /api/v1/market-briefs for approved signal distribution

### Civic Accountability System (6 Scrapers + Pattern Detection)

**Data Sources:**
- Family Court: Judge/attorney/GAL tracking, custody outcome analytics
- Property Records: Parcel/owner/valuation data
- Campaign Finance: FCPA contributions and disclosure tracking
- Commission Agendas: Meeting schedules and vendor lists
- Ethics Disclosures: Official positions and business relationships
- Municipal Contracts: Multi-city vendor and contract data

**Pattern Detection:**
- Judge bias signals: z-score >2.0 for anomalous custody rates
- GAL concentration: flag overused guardians (>60% assignment)
- Case duration outliers: z-score >3.0 for stuck/expedited cases
- Judge-attorney correlation: detect potential collusion patterns

**Use Case:** Investigate potential bias in custody outcomes (e.g., Judge Bosch + Attorney Dasinger)

## Production-Grade Infrastructure

### Root Cause Debugging (4 Critical Fixes)

**Bug 1: Crypto Churn (Symptom: re-entry within 1-2 min of stop)**
- Root Cause: Only aggregate 3-stop cooldown, no per-symbol gate
- Fix: Add 45-min per-symbol cooldown after stop-loss
- Impact: Prevents low-conviction re-entry, improves risk management

**Bug 2: Analysis Gate Reset (Symptom: analysis never fires)**
- Root Cause: In-memory last_analysis_date resets on every Render restart
- Fix: Replace with params-table-backed check (survives restarts)
- Impact: Analysis runs reliably on schedule despite infrastructure restarts

**Bug 3: Bio Watchlist No Matches (Symptom: no signals from arXiv papers)**
- Root Cause: Entity-name matching alone never works (papers describe science, not company)
- Fix: Add TOPIC_TICKER_MAP (70+ phrases mapped to 9 tickers), requires ≥2 phrase hits
- Impact: Watchlist now generates signals from relevant research

**Bug 4: Conviction Reset (Symptom: conviction resets on each restart)**
- Root Cause: Only queries current-cycle signals, loses history on restart
- Fix: Query DB for all historical signals (30-day window), accumulate conviction
- Impact: Conviction builds across restarts, more stable signal quality

### Self-Tuning System

Parameter evolution documented:
- confidence_threshold: 0.59 → 0.65 (tighten entries)
- rsi_oversold: 42.0 → 45.0 (reduce oversold sensitivity)
- cooldown_minutes: per-symbol tracking prevents churn

Conviction scoring: (positive_samples / total_samples) weighted with historical data

## Technology Stack

| Layer | Technology |
|-------|-----------|
| Trading Agents | Python (4 independent daemons) |
| Data Scrapers | Python (Playwright, BeautifulSoup, requests) |
| Database | PostgreSQL with SQLAlchemy ORM |
| Scheduler | APScheduler (cron-like scheduling) |
| Message Queue | (Future: for async processing) |
| Deployment | Render (background workers) |
| Monitoring | GitHub Actions, error logs |

## Deployment & Monitoring

**Render Background Workers:**
- walter-equity: Daily 9:30-16:00 ET trading
- walter-crypto: 24/7 trading (continuous)
- walter-bio: Daily analysis + trading
- walter-publisher: Daily 9:00 AM publish cycle

**Civic Scrapers:**
- Property records: Weekly Monday 2 AM
- Campaign finance: Weekly Wednesday 2 AM
- Commission agendas: Weekly Friday 2 AM
- Family court: Weekly Sunday 3 AM
- Pattern analysis: Weekly Sunday 4 AM

## What This Demonstrates

1. **Systematic Debugging** — 4 critical production bugs identified, root cause analyzed, fixes validated
2. **Parameter Evolution** — Data-driven tuning (confidence_threshold 0.59→0.65), documented decision rationale
3. **Complex Architecture** — 4 independent agents + civic accountability + publisher system
4. **Production Monitoring** — Real-world trading requires parameter tracking, restart resilience, rate-limiting
5. **Investigative Capability** — Civic accountability scraping shows broader technical thinking

For Recruiters: This is production trading code. Every parameter has a reason. Every fix has a before/after metric. Every restart bug has a lesson learned.

---

## Launch Timeline

Phase 1 (Current): 4 trading agents live, civic accountability ready for deployment
Phase 2: Publisher approval workflow, newsletter distribution
Phase 3: ML-powered signal fusion (combining agent opinions)

Questions? Contact via GitHub issues.
