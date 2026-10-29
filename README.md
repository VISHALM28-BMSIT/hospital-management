# Hospital Management System · Phase 1

A responsive React/Vite dashboard shell and FastAPI API foundation. All dashboard figures and schedule entries are synthetic presentation data. No patient workflows, authentication, authorization, or clinical records are implemented in this phase.

## Requirements

- Node.js 20.19+ and npm
- Python 3.11+
- Docker Desktop only if you want to run the included PostgreSQL service

## Configure

From this directory, open two PowerShell terminals.

Backend terminal:

```powershell
py -3.13 -m venv backend\.venv
backend\.venv\Scripts\python.exe -m pip install -r backend\requirements.txt
Copy-Item .env.example .env
```

Edit `.env` and replace the local development password in both `POSTGRES_PASSWORD` and `DATABASE_URL` before starting PostgreSQL. Keep the two values identical. The example is not a production secret. Start PostgreSQL when Docker is available:

```powershell
docker compose up -d db
```

Generate a patient administrator token and put the generated value in `PATIENTS_API_TOKEN` in `.env`:

```powershell
backend\.venv\Scripts\python.exe -c "import secrets; print(secrets.token_urlsafe(48))"
```

Apply the patient table migration before using the patient module:

```powershell
backend\.venv\Scripts\python.exe -m alembic -c backend\alembic.ini upgrade head
```

Start the API from the project root:

```powershell
backend\.venv\Scripts\python.exe -m uvicorn app.main:app --app-dir backend --reload --reload-dir backend --host 127.0.0.1 --port 8000
```

Frontend terminal:

```powershell
cd frontend
npm.cmd install
Copy-Item .env.example .env
npm.cmd run dev
```

Open http://127.0.0.1:5174. The dashboard API indicator reads `/api/v1/health` from FastAPI at http://127.0.0.1:8000. The health endpoint reports whether a database URL is configured; it does not claim that PostgreSQL has been contacted.

## Verify

```powershell
cd frontend
npm.cmd run build
```

```powershell
Invoke-RestMethod http://127.0.0.1:8000/api/v1/health
```

Run the patient API tests (synthetic data and isolated temporary SQLite only):

```powershell
cd backend
$env:PYTHONPATH = "$PWD\.venv\Lib\site-packages;$PWD"
.\.venv\Scripts\python.exe -m pytest
```

The patient module supports authenticated create, search, list, edit and soft-deactivation. The configured patient token is a shared administrator capability, not per-user login or full RBAC; configure a unique long token and use HTTPS outside local development. The remaining hospital modules are navigation placeholders. Do not enter real patient information into this prototype until deployment security, PostgreSQL, backups, audit policy and user-level identity are configured.