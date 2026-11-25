# Security Center integration contract — phish-guard

## Audit snapshot
- Python 3.11+ / FastAPI application with SQLAlchemy + SQLite, scikit-learn RandomForest, pandas/numpy and PhishTank/Tranco datasets.
- README documents `POST /api/v1/detect`, batch detection, history, alerts, dashboard stats and `GET /health`.
- `config/settings.py` defaults `DEBUG=True`, binds the app to `0.0.0.0`, stores data in local paths and uses a local SQLite database.
- Startup loads `data/models/phishing_model.pkl`; if absent, the API starts with `model_loaded=false`.
- The README's model metrics are project-reported and have not been independently reproduced in this audit.

## Integration role
PhishGuard is a threat-scoring provider, not the final authority for blocking a URL. Preserve the model score, source and confidence as separate fields.

## Preview adapter contract
- Internal adapter: `POST /v1/threats/phishing/check` with a validated URL.
- Normalized response: `verdict`, `confidence`, `signals[]`, `source`, `model_version`, `checked_at`.
- Do not expose history/alerts/admin routes through the public gateway.
- URL fetching, redirect following and DNS resolution must be bounded and protected against SSRF before any active probing is enabled.

## Blocking security items before service exposure
1. Set `DEBUG=False` and use explicit environment-specific settings.
2. Add service authentication, request limits, URL length limits and abuse controls.
3. Do not deserialize user-supplied pickle/model files; pin and verify the model artifact provenance.
4. Make persistence explicit and durable; local SQLite is not a multi-instance production datastore.
5. Verify the model file exists and add deterministic smoke tests with benign and known test URLs.

This repository is not ready for a public Preview endpoint until the blockers are addressed. No model training or dataset download is run by this integration PR.
