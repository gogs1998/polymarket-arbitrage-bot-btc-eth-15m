# Implementation Checklist

**Action Items from Repository Assessment**

Use this checklist to track progress on implementing the recommendations from the assessment.

---

## 🔴 Critical Priority (Do First - ~10 hours total)

### Security Fixes

- [ ] **API Key Security** (2 hours)
  - [ ] Remove API key from config.json
  - [ ] Update config.rs to read from environment variable
  - [ ] Add .env.example file with placeholder
  - [ ] Update README with secure setup instructions
  - [ ] Verify config.json is in .gitignore
  - [ ] Test that bot works with env var
  - **File:** `src/config.rs`

- [ ] **Input Validation** (2 hours)
  - [ ] Add regex patterns for slug validation
  - [ ] Add condition ID format validation
  - [ ] Add token ID validation
  - [ ] Validate all API responses before use
  - [ ] Add bounds checking for numeric inputs
  - **Files:** `src/api.rs`, `src/models.rs`

### Error Handling

- [ ] **Replace unwrap() Calls** (3 hours)
  - [ ] Fix `duration_since().unwrap()` calls (use ?)
  - [ ] Fix `Decimal::from_f64_retain().unwrap()` (handle None)
  - [ ] Fix client build `.expect()` (return Result)
  - [ ] Add retry logic for network failures
  - [ ] Implement exponential backoff for API calls
  - **Files:** `src/main.rs`, `src/api.rs`, `src/arbitrage.rs`

### Testing

- [ ] **Add Basic Tests** (3 hours)
  - [ ] Test arbitrage detection with various prices
  - [ ] Test position size calculation
  - [ ] Test profit calculation logic
  - [ ] Test safety filter (prices < $0.6)
  - [ ] Test market discovery slug generation
  - [ ] Create `tests/` directory structure
  - **New files:** `tests/arbitrage_tests.rs`, `tests/trader_tests.rs`

---

## ⚠️ High Priority (Next - ~15 hours total)

### Code Quality

- [ ] **Fix Compiler Warnings** (1 hour)
  - [ ] Remove unused import: `crate::models::Market` (main.rs:145)
  - [ ] Remove unused import: `crate::models::Market` (main.rs:177)
  - [ ] Fix unused variable: `monitor_for_trading` (main.rs:65)
  - [ ] Fix unused variable: `api_for_discovery` (main.rs:66)
  - [ ] Fix unused variable: `config` (main.rs:143)
  - [ ] Remove `mut` from `cache` (trader.rs:115)
  - [ ] Remove or use dead code (see ASSESSMENT.md section 2)
  - **Files:** `src/main.rs`, `src/trader.rs`, `src/api.rs`, `src/models.rs`

### State Management

- [ ] **Add Persistent Storage** (4 hours)
  - [ ] Add SQLite dependency to Cargo.toml
  - [ ] Create database schema for pending trades
  - [ ] Implement save_trade() function
  - [ ] Implement load_trades() function
  - [ ] Update trader.rs to persist on every trade
  - [ ] Restore state on startup
  - [ ] Test crash recovery scenario
  - **Files:** `src/trader.rs`, new `src/persistence.rs`

### Monitoring

- [ ] **Add Structured Logging** (2 hours)
  - [ ] Replace `log` with `tracing` crate
  - [ ] Add tracing subscriber configuration
  - [ ] Add spans for important operations
  - [ ] Add trace IDs for request tracking
  - [ ] Configure JSON output for production
  - **Files:** `src/main.rs`, `Cargo.toml`, all modules

- [ ] **Add Health Check** (2 hours)
  - [ ] Create HTTP server on localhost:9000
  - [ ] Implement /health endpoint
  - [ ] Return status of monitoring loop
  - [ ] Return last successful API call time
  - [ ] Add /metrics endpoint (basic)
  - **New file:** `src/health.rs`

### Operations

- [ ] **Graceful Shutdown** (2 hours)
  - [ ] Handle SIGTERM and SIGINT signals
  - [ ] Stop accepting new opportunities
  - [ ] Wait for in-flight operations
  - [ ] Save pending trades to disk
  - [ ] Log shutdown completion
  - **Files:** `src/main.rs`

- [ ] **Rate Limiting** (2 hours)
  - [ ] Add `governor` crate to Cargo.toml
  - [ ] Implement rate limiter in API client
  - [ ] Make rate limits configurable
  - [ ] Add backoff for 429 responses
  - [ ] Log rate limit events
  - **Files:** `src/api.rs`, `src/config.rs`

### Security

- [ ] **Sanitize Error Messages** (2 hours)
  - [ ] Create SanitizedError wrapper
  - [ ] Update all error logging to use wrapper
  - [ ] Ensure no API keys in logs
  - [ ] Ensure no tokens in logs
  - [ ] Test log output for sensitive data
  - **New file:** `src/errors.rs`

---

## 📋 Medium Priority (~20 hours total)

### Documentation

- [ ] **Add API Documentation** (3 hours)
  - [ ] Add rustdoc comments to all public functions
  - [ ] Add module-level documentation
  - [ ] Document all parameters and return values
  - [ ] Document error conditions
  - [ ] Run `cargo doc --open` to verify
  - **Files:** All source files

- [ ] **Add Guides** (3 hours)
  - [ ] Create CONTRIBUTING.md
  - [ ] Create DEPLOYMENT.md
  - [ ] Create TROUBLESHOOTING.md
  - [ ] Add LICENSE file (choose: MIT, Apache 2.0, etc.)
  - [ ] Update README with more examples
  - **New files:** Various .md files

### Architecture

- [ ] **HTTPS Enforcement** (1 hour)
  - [ ] Add URL validation function
  - [ ] Validate URLs are HTTPS only
  - [ ] Add host whitelist (optional)
  - [ ] Update config loading
  - **Files:** `src/config.rs`

- [ ] **Credential Verification** (1 hour)
  - [ ] Add verify_credentials() method
  - [ ] Call on startup in production mode
  - [ ] Clear error if key invalid
  - [ ] Document credential setup
  - **Files:** `src/api.rs`, `src/main.rs`

### Dependencies

- [ ] **Update Dependencies** (2 hours)
  - [ ] Update reqwest to latest
  - [ ] Update tokio-tungstenite to latest
  - [ ] Run `cargo update`
  - [ ] Run `cargo audit`
  - [ ] Test that everything still works
  - [ ] Update Cargo.lock
  - **Files:** `Cargo.toml`

- [ ] **Add CI/CD** (4 hours)
  - [ ] Create .github/workflows/ci.yml
  - [ ] Add cargo build step
  - [ ] Add cargo test step
  - [ ] Add cargo clippy step
  - [ ] Add cargo audit step
  - [ ] Configure branch protection
  - **New file:** `.github/workflows/ci.yml`

### Risk Management

- [ ] **Add Exposure Limits** (3 hours)
  - [ ] Add max_total_exposure to config
  - [ ] Track total capital at risk
  - [ ] Prevent new trades if limit reached
  - [ ] Add exposure reporting endpoint
  - **Files:** `src/trader.rs`, `src/config.rs`

- [ ] **Integer Overflow Protection** (1 hour)
  - [ ] Replace arithmetic with checked ops
  - [ ] Add validate_timestamp() function
  - [ ] Add validate_amount() function
  - [ ] Test with edge case values
  - **Files:** `src/main.rs`, `src/trader.rs`

### Performance

- [ ] **Connection Pooling** (2 hours)
  - [ ] Configure reqwest connection pool size
  - [ ] Set connection timeout
  - [ ] Set request timeout
  - [ ] Add keep-alive configuration
  - **Files:** `src/api.rs`

---

## 💡 Nice to Have (~40 hours total)

### Advanced Features

- [ ] **WebSocket Integration** (8 hours)
  - [ ] Replace polling with WebSocket subscriptions
  - [ ] Handle WebSocket reconnection
  - [ ] Parse real-time price updates
  - [ ] Benchmark latency improvement
  - **Files:** `src/monitor.rs`, new `src/websocket.rs`

- [ ] **Advanced Metrics** (6 hours)
  - [ ] Add Prometheus metrics
  - [ ] Track: opportunities, trades, profit, errors
  - [ ] Add histogram for latencies
  - [ ] Create Grafana dashboard
  - [ ] Document metrics
  - **New file:** `src/metrics.rs`, dashboard JSON

- [ ] **Backtesting** (10 hours)
  - [ ] Create historical data loader
  - [ ] Replay market conditions
  - [ ] Calculate historical performance
  - [ ] Generate performance reports
  - [ ] Compare strategies
  - **New file:** `src/backtest.rs`

- [ ] **Multi-Strategy** (8 hours)
  - [ ] Abstract arbitrage strategy interface
  - [ ] Implement alternative strategies
  - [ ] Allow running multiple strategies
  - [ ] Compare strategy performance
  - **New file:** `src/strategies/mod.rs`

- [ ] **Advanced Risk Management** (4 hours)
  - [ ] Add stop-loss logic
  - [ ] Implement Kelly Criterion for position sizing
  - [ ] Add drawdown limits
  - [ ] Position diversification
  - **Files:** `src/trader.rs`, new `src/risk.rs`

- [ ] **Deployment** (4 hours)
  - [ ] Create Dockerfile
  - [ ] Create docker-compose.yml
  - [ ] Create Kubernetes manifests
  - [ ] Document deployment process
  - **New files:** `Dockerfile`, `k8s/`, etc.

---

## 📊 Progress Tracking

### Summary Statistics

- **Total Items:** 70+
- **Estimated Total Time:** ~85 hours
- **Critical (Must Do):** 4 sections, ~10 hours
- **High Priority:** 8 sections, ~15 hours
- **Medium Priority:** 9 sections, ~20 hours
- **Nice to Have:** 6 sections, ~40 hours

### Completion Status

Create marks as you complete items:

```
Critical:  [ ] [ ] [ ] [ ]  (0/4)
High:      [ ] [ ] [ ] [ ] [ ] [ ] [ ] [ ]  (0/8)
Medium:    [ ] [ ] [ ] [ ] [ ] [ ] [ ] [ ] [ ]  (0/9)
Nice to Have: [ ] [ ] [ ] [ ] [ ] [ ]  (0/6)

Overall: 0% complete
```

---

## 🎯 Quick Start Path

If you have limited time, focus on this minimal path:

### Week 1 (Critical Items)
1. API key security (2h)
2. Replace unwrap() calls (3h)
3. Add basic tests (3h)
4. Input validation (2h)

**Result:** Production-safe basics

### Week 2 (Core Reliability)
5. Fix compiler warnings (1h)
6. Persistent storage (4h)
7. Graceful shutdown (2h)
8. Rate limiting (2h)

**Result:** Reliable operation

### Week 3 (Observability)
9. Structured logging (2h)
10. Health check endpoint (2h)
11. Sanitize errors (2h)

**Result:** Monitorable system

### Week 4 (Hardening)
12. HTTPS enforcement (1h)
13. Credential verification (1h)
14. Update dependencies (2h)
15. Add CI/CD (4h)

**Result:** Production-ready

---

## 📝 Notes

- Mark items as complete: `- [x]`
- Add completion dates in comments
- Note any deviations from plan
- Track time spent vs estimated
- Document any blockers

---

## 🔗 References

- [Full Assessment](ASSESSMENT.md)
- [Security Guide](SECURITY.md)
- [Quick Summary](ASSESSMENT_SUMMARY.md)

---

**Created:** 2026-01-13  
**Last Updated:** 2026-01-13
