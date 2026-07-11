# Walter Architecture

Multi-Agent: Equity (RSI+VWAP), Crypto (volatility-tuned), Bio (research catalyst), Publisher (human approval)

Root Cause Bugs Fixed:
1. Crypto Churn: Per-symbol 45min cooldown (was aggregate only)
2. Analysis Gate: DB-backed state (was in-memory, reset on restart)
3. Bio Watchlist: Topic-phrase map (entity-only never worked)
4. Conviction Reset: 30-day historical (was current cycle only)

Database (PostgreSQL): daily_summary, positions, trades, stop_loss_times, civic tables

Deployment: Render workers (equity daily, crypto 24/7, bio daily, publisher daily)
Civic Scrapers: Scheduled weekly

Performance: Equity ~50-100/mo, Crypto tuned lower, Bio ~10-20/mo
