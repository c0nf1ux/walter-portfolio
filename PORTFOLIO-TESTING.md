# Walter: Testing & Validation

Tests: Unit (50), Integration (30), Publisher (20) = 100 total, focus on correctness

Paper Trading: Real market data, simulated money, 1-2 weeks validation before live

Parameter Validation: Backtest 90 days → paper trade 1 week → compare metrics → document → commit

Risk Limits: Stop-loss -8% (tested), daily -10% (tested), cooldown 45min (tested), conviction 0.3 (tested)

Quality Gate: All tests pass, parameters documented, risk limits verified
