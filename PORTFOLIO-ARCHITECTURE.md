# Walter Architecture

## System Overview

4 Trading Agents (Python) + 6 Civic Scrapers (Python) + Publisher System (Node.js API) → PostgreSQL DB + Render Background Workers

## Multi-Agent Architecture

### Equity Agent (main.py)
RSI + VWAP mean reversion strategy
- Daily rebalancing 9:30-16:00 ET
- Stop-loss at 8%
- Per-symbol cooldown after stop (45 min)
- Watermark tracking for dynamic rebalancing
- Conviction scoring based on signal strength

### Crypto Agent (main_crypto.py)
Same strategy, volatility-tuned
- 24/7 trading (no market hours)
- Higher RSI thresholds (0.65 vs 0.59)
- Per-symbol cooldown (BTCUSD/ETHUSD keyed)
- Parameter evolution: confidence_threshold 0.59→0.65, rsi_oversold 42→45

### Biomimicry Agent (main_bio.py)
Catalyst trading on scientific breakthroughs
- arXiv + bioRxiv scraping
- Topic-ticker map: 70+ phrases → 9 tickers
- Requires ≥2 phrase hits (avoid false positives)
- Entity matching prioritized over topic matching
- Conviction accumulation across scrape cycles

### Publisher (walter-publisher)
Signal aggregation + content generation
- Fuse signals from all 3 trading agents
- Conviction scoring: 30-day historical window
- Content generator: newsletter briefs
- Dashboard: human approval workflow
- API: /api/v1/market-briefs (approved signals)

## Database Schema (PostgreSQL)

### Trading Tables
daily_summary: Symbol, date, PnL, realized_gain, position_value
positions: Symbol, quantity, entry_price, current_price, stop_loss_time
trades: Symbol, date, entry_price, exit_price, quantity, pnl
stop_loss_times: Symbol, last_stop_trigger (cooldown tracking)
last_reset_date: Timestamp of latest state reset (tracks restart resilience)

### Signal Tables (Bio Agent)
raw_signals: raw_url (unique), text, source, matched_tickers[], created_at
bio_signals: ticker, conviction, matched_count, last_updated
matched_tickers TEXT[] (PostgreSQL array)

### Civic Tables
property_records: parcel_id (unique), owner, address, valuation, tax_id
campaign_finance: fcpa_id (unique), candidate, contributor, amount, date
family_court: docket_number (unique), judge, attorney, gal, outcome, case_type
commission_agendas: meeting_id (unique), date, agenda, minutes, vendors
ethics_disclosures: filing_id (unique), official, position, interests, relationships
municipal_contracts: contract_id (unique), vendor, amount, scope, city, approving_official

### Pattern Detection
judge_stats: Judge, total_cases, custody_mother_rate, custody_father_rate, avg_duration, gal_assignments
pattern_alerts: alert_type, severity, judge_name, finding, confidence

## Civic Data Scrapers

### Scraper Architecture (Base)
Abstract base: fetch → parse → store pipeline
Metadata tracking: source, status, error_logs
Deduplication: prevent duplicate entries
Rate limiting: Playwright delays, BeautifulSoup timeouts

### Property Records Scraper
Source: Baldwin County Revenue Commission
Fetch: HTTP + parsing real estate database
Dedup: parcel_id unique constraint
Update: overwrite existing, don't duplicate

### Campaign Finance Scraper
Source: Alabama FCPA portal
Fetch: Form filling, pagination, date/contributor extraction
Dedup: fcpa_id unique constraint

### Family Court Scraper
Source: Alabama Unified Judicial System
Fetch: Form filling, pagination, judge/attorney filtering
Dedup: docket_number unique constraint
Analysis: custody_outcome z-score, judge bias signals

### Pattern Detector
Judge statistics: Aggregate outcomes by judge
Bias signals: z-score >2.0 for custody rate anomalies
GAL concentration: Flag >60% reuse
Case duration: z-score >3.0 for outliers

## Root Cause Bugs & Fixes

### Bug 1: Crypto Churn (Per-Symbol Cooldown)
Symptom: Re-entry within 1-2 minutes of stop
Root Cause: Only aggregate cooldown, no per-symbol tracking
Fix: Add stop_loss_times[symbol] = now + 45min
Impact: Prevents low-conviction re-entry

### Bug 2: Analysis Gate Reset (State Persistence)
Symptom: Analysis never fires
Root Cause: In-memory last_analysis_date resets on Render restart
Fix: Store in params table, check against today UTC, write after success
Impact: Analysis runs reliably despite infrastructure restarts

### Bug 3: Bio Watchlist Failure (Topic Matching)
Symptom: No signals from arXiv papers
Root Cause: Entity-name matching alone doesn't work
Fix: Add TOPIC_TICKER_MAP (70+ phrases), require ≥2 hits
Impact: Watchlist now generates signals

### Bug 4: Conviction Reset (Historical Accumulation)
Symptom: Conviction resets on restart
Root Cause: Only queries current-cycle signals
Fix: Query DB for all 30-day historical signals
Impact: Conviction builds across restarts

## Deployment Architecture

Render Background Workers:
- walter-equity: Daily 9:30-16:00 ET
- walter-crypto: 24/7
- walter-bio: Daily
- walter-publisher: Daily 9:00 AM

Civic Scrapers (Scheduled):
- Property: Monday 2 AM
- Campaign: Wednesday 2 AM
- Agendas: Friday 2 AM
- Family Court: Sunday 3 AM
- Pattern Analysis: Sunday 4 AM

## Monitoring & Alerts

### What We Track
daily_summary: P&L, realized gains, position values
stop_loss_times: Cooldown tracking
conviction: Signal quality over 30 days
scrape_status: Success/failure, error logs

### Alert Thresholds
CRITICAL: P&L < -10% in single day
HIGH: Analysis gate missed schedule
MEDIUM: Scraper failed (retry next cycle)
LOW: Conviction below 0.3 (low-conviction trades)

## Performance Characteristics

Equity Agent: ~50-100 trades/month, avg 14.3 sec/cycle (fully loaded)
Crypto Agent: ~48+ trades/9 days, parameter-tuned for lower frequency
Bio Agent: ~10-20 signals/month, requires ≥2 phrase hits
Publisher: 1 digest/day, 3-5 approved signals

Database: PostgreSQL, connection pooling, query optimization on indices
Scheduler: APScheduler, 7 scheduled jobs, max concurrency 2 (no race conditions)
