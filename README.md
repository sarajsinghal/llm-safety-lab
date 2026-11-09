# LLM Safety Lab

A portfolio-ready, local-first MVP for reviewing language-model responses with transparent security and quality heuristics. It supports English and Hindi starter probes, highlights findings with evidence and recommendations, and exports a JSON report.

## MVP and architecture

```text
User supplies prompt, response, and optional trusted context
                 │
                 ▼
       Streamlit UI (app.py)
                 │
                 ▼
  Pure evaluation function (llm_eval_agent/evaluator.py)
       ├── injection/jailbreak phrase checks
       ├── PII pattern checks
       ├── unsafe-output phrase checks
       ├── context term-overlap signal
       └── response completeness/uncertainty cues
                 │
                 ▼
       Findings + heuristic indicators → JSON download
```

No provider integration or API key is required. The evaluation function is provider-independent, making a future model-judge adapter straightforward. No result is generated until you supply a response and run the checks.

## Setup

Python 3.10 or newer is recommended.

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
streamlit run app.py
```

Open the local URL Streamlit prints. On macOS/Linux, activate with `source .venv/bin/activate`.

## Using the app

1. Select an English or Hindi sample, or enter your own prompt, response, and optional trusted context.
2. Click **Run evaluation**.
3. Review the matched text, category, severity, and suggested follow-up.
4. Download the JSON report for review or portfolio demonstration.

The built-in PII sample uses synthetic values. Avoid entering actual secrets or personal information in demos.

## Checks and scoring

The app uses deterministic phrase/pattern rules and a context token-overlap signal. Each category is an indicator from 0–100, where higher is better. For categories with findings, the category score is the lowest associated rule score; the overall indicator is the unweighted mean of five categories. These numbers are not probabilities, validated safety scores, or model benchmark results. Groundedness is not semantically assessed: lexical overlap can miss paraphrases and cannot establish that a claim is supported. An empty match list does not prove safety. English and Hindi phrase coverage is intentionally small.

## Run the checks

```powershell
python -m unittest discover -s tests -v
```

## Deployment

For a quick private demo, run the local command above or deploy to a Streamlit-compatible host using `requirements.txt` and `streamlit run app.py`. Before exposing it publicly, add authentication, request-size limits, retention controls, and secret redaction; review the host's logging and privacy settings. Do not present heuristic results as proof of safety.

## Roadmap

- Add provider adapters only after selecting a provider; read credentials from environment or a managed secrets store.
- Add a documented, labeled English/Hindi test corpus and track false positives/negatives.
- Add a separately reported LLM judge for semantic groundedness and policy compliance, with prompt/version tracking and human review.
- Add reproducible batch evaluation and aggregate metrics after benchmark data exists.
