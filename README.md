# Student Organization Operations Portal

**CSE 6324 · Team 7 · Final submission**

A web portal for running a student organization: membership applications and approval, two roles (member and
officer_admin), events with RSVPs and attendance, committees and tasks, announcements and shared document links,
in-app notifications, engagement reports, and an AI meeting-notes summary.

| | |
|---|---|
| Repository | https://github.com/Likhithap76/student-org-portal |

## Team
| Member | Focus | Main work |
|---|---|---|
| Andrew Ball | Database and planning | ERD and PostgreSQL schema on Supabase, synthetic seed data, sprint tracking |
| Likhitha Pericharla | Backend, integration and quality | Spring Boot microservices and gateway, role authorization, API contracts, backend tests, static code analysis (PMD/CPD), CI for backend tests |
| Rhea Malpekar | Frontend, AI and report | React UI, Ollama AI integration and evaluation, report and evidence compilation |
| Jindagi Patel | QA and Selenium | Manual, Selenium and API acceptance testing |

## Contents of this ZIP
```
Team07_StudentOrganizationOperationsPortal_Final/
├── Final_Report/          Team07_Final_Report.pdf
├── Source_Code/
│   ├── backend/           Spring Boot microservices and API gateway (see backend/README.md)
│   ├── frontend/          React + TypeScript web app (see frontend/README.md)
│   └── docs/              API docs (OpenAPI), backend test document, team test evidence
├── Tests/
│   ├── test_cases/        Written test cases and test matrices
│   ├── test_scripts/      Automated test scripts
│   ├── test_data/         Synthetic test data
│   └── test_results/      NFR evidence (responsiveness, permissions, data handling, reliability, maintainability)
├── Documentation/
│   ├── design/            Architecture diagram, ERD
│   ├── planning/          Burndown chart, Gantt timeline, velocity report
│   ├── ai_evidence/       AI meeting-summary evaluation and screenshots
│   └── screenshots/       Final application screenshots
└── README.md
```
Config template: `Source_Code/backend/.env.example` (safe placeholders only; copy it to `.env` and fill in your values).
Dependency files: `Source_Code/backend/pom.xml` and `mvnw` (Maven wrapper), `Source_Code/frontend/package.json`.
Automated backend tests are in `Source_Code/backend/<module>/src/test/java/`.

## Architecture
```
Browser ──► Frontend (Vite, :5173) ──► API Gateway :8080 (/api, JWT) ──► Services :8081–8087 ──► PostgreSQL (Supabase)
                                                                              └─ ai-service ──► Ollama (llama3.2:3b)
```
Services: user (8081), event (8082), task (8083), content (8084), notification (8085), reporting (8086), ai (8087).

## Tech stack
| Part | Technology |
|---|---|
| Backend | Java 21, Spring Boot 3.3, Spring Cloud Gateway, Spring Security (JWT, BCrypt), Maven |
| Frontend | React 18, TypeScript, Vite |
| Database | PostgreSQL on Supabase; H2 for local runs and tests |
| AI | Ollama with llama3.2:3b (local model) |
| Testing and CI | JUnit 5, Mockito, MockMvc, JaCoCo, PMD/CPD, GitHub Actions |

## How to run
**Prerequisites:** Java 21, Node.js 18 or later. Ollama only for the AI summary.

1. **Backend** (from `Source_Code/backend`):
   ```bash
   cp .env.example .env    # fill in JWT_SECRET, INTERNAL_SERVICE_TOKEN, SEED_ADMIN_EMAIL, SEED_ADMIN_PASSWORD
   ./run-all.sh            # starts all services; stop with ./stop-all.sh
   ```
   Without database settings it uses a local H2 database. For Supabase, set `DB_URL`, `DB_USER`, `DB_PASSWORD`
   in `.env` and run `SPRING_PROFILES_ACTIVE=supabase ./run-all.sh`.
2. **Frontend** (from `Source_Code/frontend`, in a second terminal):
   ```bash
   npm install
   npm run dev
   ```
3. Open http://localhost:5173 and log in with the seed admin from `.env`. API docs: http://localhost:8080/swagger-ui.html
4. **AI summary:** install Ollama, run `ollama pull llama3.2:3b`, set `LLM_BASE_URL=http://localhost:11434`
   and `LLM_MODEL=llama3.2:3b` in `.env`, then restart the backend. Without it, the AI page shows a clear
   "AI service is unavailable" message and everything else works.

## Testing
| What | How | Result |
|---|---|---|
| Automated backend tests | `./mvnw verify` in `Source_Code/backend` | 525 tests pass; 100% line coverage on service and controller packages (gate 70%) |
| Static analysis | `./mvnw compile pmd:pmd pmd:cpd` | 0 issues, 0 duplications across 9 modules |
| Manual system tests | Through Swagger on Supabase (`Tests/test_cases/`) | 60 of 60 passed |
| Nonfunctional requirements | `Tests/test_results/NFR/` | See the final report, section 2.4 |
| AI evaluation | `Documentation/ai_evidence/` | 5 synthetic cases, human review required |
| CI | GitHub Actions on every push to `backend/` | Green on the final commit |
