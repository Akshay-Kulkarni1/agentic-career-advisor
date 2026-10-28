# Local development setup

This guide is for the `agentic-career-advisor` repository root, not the old `aws-agetic-ai` folder name.

## Prerequisites

- Python 3.11+ and pip
- Node.js 20+ recommended (frontend Vite configuration and dependencies)
- Chrome/Chromium if experimenting with the optional Selenium integration
- Optional AWS credentials/profile with access to an enabled Bedrock model, if testing live model inference

## 1. Python environment

```bash
python3 -m venv .venv
source .venv/bin/activate  # macOS/Linux; Windows PowerShell: .venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

## 2. Local configuration

```bash
cp .env.example .env
```

Edit `.env` as needed. Use an AWS profile or IAM role wherever possible; do not paste credentials into tracked files. The SDK uses `AWS_REGION` from the application config (default `us-east-2`). `AWS_DEFAULT_REGION` may also be useful for AWS CLI commands but does not replace the app's `AWS_REGION` setting.

The example Bedrock model setting uses a US geographic inference profile, which may route within US regions and requires the appropriate IAM permissions and model access. If you require in-region-only processing, configure a supported in-region model instead.

For local Vite development, `frontend/.env.development` points API calls to `http://127.0.0.1:8000`. `frontend/.env.production` contains an empty URL for same-origin deployments.

## 3. Course dataset

The source ZIP contains `data/utd_courses.json` with 2,470 course records from a UT Dallas catalog. Those descriptions have **not** been cleared for redistribution, so the data file should stay local and is excluded from the GitHub upload.

For a local run using data you are authorized to use, place a compatible JSON file at `data/utd_courses.json`:

```json
{
  "courses": [
    {
      "prefix": "DEMO",
      "number": "1001",
      "title": "Example Course (illustrative only)",
      "description": "Illustrative content written for testing.",
      "credits": "3",
      "prerequisites": null
    }
  ]
}
```

The sample above is fictional and not an actual UT Dallas course. Without the file, the backend starts but catalog-dependent recommendations are limited. Consult [`data/README.md`](data/README.md).

## 4. Start backend and frontend

Terminal A (repository root):

```bash
python start_server.py
# alternatively: uvicorn src.api.app:app --reload --host 127.0.0.1 --port 8000
```

Terminal B:

```bash
cd frontend
npm ci
npm run dev
```

UI: <http://localhost:3000>  
OpenAPI docs: <http://127.0.0.1:8000/docs>  
API health: <http://127.0.0.1:8000/health>

## 5. Basic smoke tests

```bash
curl -f http://127.0.0.1:8000/health
curl -f http://127.0.0.1:8000/api/onboarding/options
curl -X POST http://127.0.0.1:8000/api/onboarding/quick-start \
  -H 'Content-Type: application/json' \
  -d '{"career_goal":"Software Engineer","current_skills":["Python"]}'
```

Results may be incomplete when the local catalog, browser integration or Bedrock permissions are unavailable. A passing `/health` only shows that the HTTP process responded.

## Troubleshooting

- `ModuleNotFoundError: src.core.aws...`: ensure `src/core/aws/bedrock_service.py` is present. Earlier `.gitignore` rules incorrectly ignored any directory named `aws`.
- Missing courses: provide an authorized `data/utd_courses.json` locally; no catalog is bundled on GitHub.
- Bedrock inference fails: check your AWS identity, model availability, region, and Bedrock authorization. Rule-based fallbacks may still work.
- Selenium fails: check browser/driver compatibility and manual login constraints; live scraping is not guaranteed.
- Frontend API errors: start the backend before the frontend and verify `VITE_API_URL` or the Vite proxy settings.
- Avoid production deployments with the current CDK scripts until you've reviewed ingress, IAM policies, domain/TLS, CORS and external-service behavior.
