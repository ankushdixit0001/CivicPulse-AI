# CivicPulse AI

AI-assisted civic issue reporting, triage, and authority dispatch command center.

CivicPulse is a prototype with three services:

- **Frontend:** React 18 + Vite citizen, worker, and admin portals.
- **Backend:** Node.js + Express, MongoDB/Mongoose, and Socket.IO.
- **AI vision:** Python + FastAPI, Ultralytics YOLO, and OpenCV.

## Requirements

- Docker Engine and Docker Compose v2 (recommended), or
- Node.js 22+, Python 3.11+, and MongoDB 7+ for manual development.

## Quick start with Docker

1. Clone the repository.
2. Copy `.env.example` to `.env`.
3. Set a unique, long `AUTH_SECRET` in `.env`.
4. From the repository root, run:

   ```bash
   docker compose up --build
   ```

5. Open the application:

   - Frontend: http://localhost:3000
   - Backend health: http://localhost:4000/health
   - AI health: http://localhost:5000/health

To stop the services, run `docker compose down`. To also remove the local MongoDB volume, use `docker compose down -v` (this permanently deletes the local database data).

The first AI inference may download the YOLO model if it is not already available in the image/container. Ensure the AI service has outbound network access for that initial download.

## Manual development

Start MongoDB separately, then run each service in its own terminal.

**1. AI vision service**

```bash
cd ai-vision-service
python -m venv .venv
# Windows PowerShell: .venv\\Scripts\\Activate.ps1
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload --host 0.0.0.0 --port 5000
```

**2. Backend**

```bash
cd backend-service
npm ci
npm run dev
```

**3. Frontend**

```bash
cd frontend-app
npm ci
npm run dev
```

For manual development, set the backend environment variables in the repository-root `.env` file. The frontend uses `VITE_API_BASE_URL` and `VITE_SOCKET_URL`; Vite reads these when the development server starts or the production bundle is built.

## Port and environment configuration

| Service | Default URL / port | Main settings |
|---|---|---|
| Frontend | http://localhost:3000 | `VITE_API_BASE_URL`, `VITE_SOCKET_URL` |
| Backend | http://localhost:4000 | `PORT`, `MONGODB_URI`, `AI_SERVICE_URL`, `CORS_ORIGIN`, `AUTH_SECRET` |
| AI vision | http://localhost:5000 | `AI_SERVICE_PORT` |
| MongoDB | localhost:27017 | `MONGODB_URI` |

Docker Compose overrides the internal service URLs so containers communicate using their Compose service names. The frontend API/socket URLs are build-time values because the frontend is served as static files by Nginx.

## Authentication

The application provides separate portals:

- Citizen: `/login/citizen` or `/signup`
- Field worker: `/login/worker`
- Admin: `/login/admin`
- Command center: `/command-center`

Public signup creates citizen accounts. Worker and admin accounts are provisioned by the service. Seeded development accounts are documented in the project brief; change development credentials and use a strong `AUTH_SECRET` before any shared or deployed environment.

## Current prototype limitations

CivicPulse is not production-ready. AI labels and severity are experimental; validate model output before operational use. Review the project brief for known gaps around live dashboard updates, map interactions, image privacy/storage, duplicate merging, authorization, and repair verification. Do not treat fallback/demo tickets or fallback AI detections as real incidents.

## Workspace scripts

From the repository root:

- `npm run dev:backend`
- `npm run dev:frontend`
- `npm run dev:ai`
- `npm run build:frontend`

