# Agentic Career Advisor

A prototype career-guidance application built with **FastAPI** and **React/TypeScript**. It coordinates job-market, course-catalog, career-matching, and project-advisor components to help students explore possible careers and learning paths. The original project focuses on University of Texas at Dallas (UT Dallas) coursework.

> **Project status:** Demonstration/prototype. Recommendations are informational, not official academic advising or verified real-time job-market intelligence. Live job scraping, AWS Bedrock model calls, and the course catalog depend on local configuration and external services.

## Features

- Quick-start and comprehensive career-interest forms
- Career and skill-gap analysis through a multi-agent orchestrator
- Course lookups and recommendations when a compatible local catalog is available
- Portfolio-project suggestions, including a rule-based fallback
- Job-market analysis using a LinkedIn/Selenium integration when available; results may use cached or fallback data
- Learning-path and results dashboard

## Stack

| Layer | Technologies |
| --- | --- |
| UI | React 18, TypeScript, Vite, Tailwind CSS, Axios |
| API | Python 3.11+, FastAPI, Uvicorn |
| Agents | Career matching, course catalog, job market, project advisor |
| Optional integrations | AWS Bedrock (boto3), Selenium/Chrome |
| Data | Local JSON catalog (not redistributed in this repository) |

## Repository layout

```text
agentic-career-advisor/
├── src/
│   ├── agents/                # Multi-agent logic
│   ├── api/                   # FastAPI routes and onboarding models
│   ├── auth/                  # LinkedIn login/session helper
│   ├── config/                # Environment and AWS configuration
│   ├── core/aws/              # Bedrock service (required source module)
│   ├── core/llm/              # Career analysis / fallbacks
│   └── scrapers/              # Selenium-based scrapers
├── frontend/                  # React / Vite application
├── data/README.md            # Catalog sourcing and local-data guidance
├── requirements.txt
├── .env.example
├── start_server.py
└── SETUP.md
```

## Local setup

1. Clone the repository and enter its root:

   ```bash
   git clone https://github.com/Akshay-Kulkarni1/agentic-career-advisor.git
   cd agentic-career-advisor
   ```

2. Create a Python virtual environment and install dependencies:

   ```bash
   python3 -m venv .venv
   source .venv/bin/activate     # macOS / Linux
   python -m pip install -r requirements.txt
   ```

3. Configure optional integrations:

   ```bash
   cp .env.example .env
   ```

   Edit `.env` locally and **never commit real credentials**. Prefer AWS profiles or IAM roles over permanent access keys. Model access is required for live Bedrock inference; otherwise some flows use fallbacks. The example model uses US geographic cross-region inference—review AWS routing, costs and required permissions before enabling it.

4. Launch the API from the repository root:

   ```bash
   python start_server.py
   ```

   API docs: <http://127.0.0.1:8000/docs> · Health: <http://127.0.0.1:8000/health>

5. In another terminal, run the web app:

   ```bash
   cd frontend
   npm ci
   npm run dev
   ```

   Open <http://localhost:3000> (configured in `frontend/vite.config.ts`). The default development frontend configuration points to <http://127.0.0.1:8000>.

**Important:** The full course dataset is intentionally *not* in GitHub. To enable course features, place an authorized catalog at `data/utd_courses.json` following the format described in [`data/README.md`](data/README.md). Without it, course features return limited/empty results. See [SETUP.md](SETUP.md) for troubleshooting and constraints.

## Selected API endpoints

| Method | Path | Purpose |
| --- | --- | --- |
| `GET` | `/health` | Process health (not proof of Bedrock connectivity) |
| `GET` | `/docs` | FastAPI interactive API documentation |
| `GET` | `/api/onboarding/options` | Form options |
| `POST` | `/api/onboarding/quick-start` | Career guidance from a short profile |
| `POST` | `/api/onboarding/comprehensive` | Guidance from a detailed profile |
| `POST` | `/api/career-guidance` | Legacy guidance endpoint; LinkedIn auth check |
| `POST` | `/job-market` | Job-market analysis |
| `POST` | `/course-search` | Catalog search |
| `POST` | `/api/project-recommendations` | Project recommendations |
| `GET` | `/api/agents/status` | Agent status |

Example: quick start

```bash
curl -X POST http://127.0.0.1:8000/api/onboarding/quick-start \
  -H 'Content-Type: application/json' \
  -d '{"career_goal":"Data Scientist","current_skills":["Python"],"location":"Dallas, TX"}'
```

## Data and privacy

- `data/linkedin_session.json` stores local auth-state information; it is excluded from version control. The existing timestamp-based session status is **not** a secure session-verification mechanism.
- Never commit real AWS keys, LinkedIn credentials, `.env`, deployment logs, or generated infrastructure outputs.
- LinkedIn access and scraping are subject to service availability, policy and rate limits; no guarantee is made that every request returns fresh postings.
- UT Dallas catalog descriptions may be copyrighted. The original dataset is not published here pending redistribution review. The university's current official course catalog is at <https://catalog.utdallas.edu/>.

## Known limitations

- No automated end-to-end test suite or verified production deployment is included in this review.
- Career matching currently uses rule-based fallback logic for part of its analysis; having AWS credentials does not mean every recommendation comes from an LLM.
- Without the licensed/authorized local course dataset, course lookups and matches will be incomplete.
- Job-scraper/browser interaction may require manual LinkedIn sign-in, may fail in headless deployments, and should not be treated as an authenticated production service.
- AWS CDK deployment configuration needs a separate infrastructure/security review and is not part of the recommended initial release.

## License

Project source code is distributed under the **Apache License 2.0**; see [LICENSE](LICENSE). This does not grant rights to third-party catalog content or services.
