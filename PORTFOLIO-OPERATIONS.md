# Walter: Operations & Deployment

## Deployment Architecture

Render Background Workers:
- walter-equity: Daily 9:30-16:00 ET
- walter-crypto: 24/7
- walter-bio: Daily morning
- walter-publisher: Daily 9:00 AM

Civic Scrapers (Scheduled via APScheduler):
- Property Records: Monday 2 AM
- Campaign Finance: Wednesday 2 AM
- Commission Agendas: Friday 2 AM
- Family Court: Sunday 3 AM
- Pattern Analysis: Sunday 4 AM

Database: PostgreSQL (separate instance)

## Parameter Tuning Process

Documented workflow:
1. Query daily_summary via psycopg2
2. Analyze P&L, trade frequency, conviction
3. Identify issue (e.g., 48 trades/9d, low conviction)
4. Adjust parameter (confidence_threshold 0.59 → 0.65)
5. Backtest on historical data
6. Paper trade for 1 week
7. Compare metrics before/after
8. Commit with rationale + metrics

Example: Crypto tuning (2026-05-18)
- Before: 48 trades/9d, $0 realized P&L
- Change: confidence_threshold 0.59→0.65, rsi_oversold 42→45
- After: Lower frequency, better conviction

## Monitoring

Metrics: P&L, realized_gain, conviction, stop_loss_times, last_analysis_date
Alerts:
- CRITICAL: P&L < -10% (circuit breaker)
- HIGH: Analysis gate missed (>24h late)
- MEDIUM: Scraper failure (retry next cycle)
- LOW: Conviction < 0.3 (low-confidence trade)

## Cost Analysis

Render: ~$50/mo (4 background workers)
PostgreSQL: ~$15/mo
API fees: Variable
Total: ~$65+/month

## Scaling (Future)

Phase 1: 4 agents, 5 civic scrapers (current)
Phase 2: ML signal fusion
Phase 3: International markets
Phase 4: Multi-account orchestration

## Performance Targets

Equity: ~50-100 trades/month
Crypto: Tuned to lower frequency
Bio: ~10-20 signals/month
Publisher: 1 digest/day
Query latency: <500ms (indexed by symbol, date)
