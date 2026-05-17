# AI_FORECASTING-
# ARGUS V3 — Multi-Agent Forecasting System

Hawkes Processes + Conformal Prediction for Prediction Markets  
Prophet Arena Hackathon — Forecasting Track**

Quick Start (for evaluators)

# 1. Clone / unzip
cd argus_submission

# 2. Install
pip install -r requirements.txt

# 3. Start the OpenAI-compatible API server
./run.sh
# → Listening on http://0.0.0.0:8000
# → POST /v1/chat/completions

# 4. Test it
curl -X POST http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"messages":[{"role":"user","content":"Will this acquisition close by Q3?"}]}'


Optional modes:
```bash
./run.sh demo       # local forecast demo (no server)
./run.sh validate   # 50-trial Monte Carlo calibration proof
./run.sh backtest   # 25-question backtest harness

What This Does

ARGUS reads what informed actors do (trade timing, anomaly patterns) and outputs a calibrated probability forecast with a mathematically-guaranteed 90% confidence interval.

The key insight: Prediction markets underprice events where insiders can't speak but can trade. We decode those trades.

---

Architecture — 5 Agent Layers

Raw signals (timestamps, anomaly scores)
  ↓
Layer 1: Hawkes Process Agent        — models self-exciting trade cascades
  ↓
Layer 2: Basis Expansion Agent       — captures right-tail informed-actor behavior
  ↓
Layer 3: Gaussian Process Agent      — smooths intensity + quantifies uncertainty
  ↓
Layer 4: Conformal Prediction Agent  — guarantees 90% CI coverage (Vovk 2015)
  ↓
Layer 5: Sequential Bayesian Filter  — online posterior as new signals arrive
  ↓
Output: { probability, ci_lower, ci_upper, reasoning }




## API Reference

### `POST /v1/chat/completions`

OpenAI-compatible. Send the prediction market question as the last user message.

**Request:**
```json
{
  "model": "argus-v3",
  "messages": [
    {"role": "user", "content": "Will the FDA approve drug X by end of year?"}
  ]
}


Response:
```json
{
  "choices": [{
    "message": {
      "role": "assistant",
      "content": "{\"probability\": 0.73, \"ci_lower\": 0.58, \"ci_upper\": 0.88, \"confidence_interval\": \"90%\", \"reasoning\": \"...\"}"
    }
  }]
}




- Hawkes, A. G. (1971). Spectra of some self-exciting random processes.
- Vovk, V. (2015). Algorithmic Learning in a Random World (conformal prediction).
- Rasmussen & Williams (2006). Gaussian Processes for Machine Learning.
