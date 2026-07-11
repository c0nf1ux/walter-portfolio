# Walter: Key Decisions & Trade-Offs

## 1. Multi-Agent Architecture

Decision: 4 independent agents (equity, crypto, biomimicry, publisher).

Why: Each has different risk profile, market hours, tuning needs. Isolation prevents cascade failures.

Result: Equity stable, crypto volatile-tuned, bio research-focused, publisher human-approved.

## 2. Civic Accountability Integration

Decision: 6 data scrapers (family court, campaign finance, property, contracts, ethics, agendas).

Why: Research drives investing. Civic data reveals market inefficiencies. Judge bias, contract patterns matter.

Result: Judge-attorney correlation analysis, civic data fusion with trading signals.

## 3. Publisher with Human Approval

Decision: All signals require human review before publishing.

Why: Autonomous trading is risky. Human judgment catches tail risks.

Result: Dashboard-based approval workflow, curated newsletter, /api/v1/market-briefs.

## 4. Parameter Evolution (Documented)

Decision: Track every parameter change with metrics.

Why: confidence_threshold 0.59→0.65 is data-driven response to 48 trades/9d showing low conviction.

Result: RSI tuning, per-symbol cooldown, conviction accumulation over 30 days.

## 5. State Persistence (Restart Resilience)

Decision: Store analysis state in DB, not memory.

Why: Render restarts daily. Memory state would reset, skipping analysis.

Result: Analysis runs reliably despite infrastructure restarts.

## 6. Conviction Scoring (Historical Window)

Decision: Accumulate signals over 30-day window, not current cycle.

Why: Single signals are noise. Historical accumulation reveals patterns.

Result: Stronger signals, less churn, better risk management.

## Summary

Balance risk management (human approval, parameter tuning) with automation (agents, conviction, civic data).
