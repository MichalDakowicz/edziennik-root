# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Modéa** is a full-stack Polish school management system (electronic gradebook / dziennik elektroniczny) with role-based access for students, teachers, and parents. It consists of:

- **Backend**: `edziennik/` — Django 5.2 + Django REST Framework, PostgreSQL
- **Frontend**: `edziennik-frontend/` — React 18 + TypeScript, Vite, Tailwind CSS

## Commands

### Frontend (`edziennik-frontend/`)

```bash
npm run dev          # Dev server on port 5173
npm run build        # TypeScript check + Vite build
npm run test         # Run tests once (Vitest)
npm run test:watch   # Watch mode
npm run test:coverage
```

### Backend (`edziennik/`)

```bash
python manage.py runserver       # Dev server on port 8000
python manage.py test            # Full test suite
python manage.py test <app>      # Single app (e.g. grades, attendance)
python manage.py migrate
python tools/populate_db.py --reset  # Load demo data
```

### Docker — dev (from `edziennik/`)

```bash
docker compose up                          # local postgres + backend + frontend
docker compose up backend frontend         # skip local DB (when DATABASE_URL set in .env)
docker compose logs -f backend             # tail logs
```

Ports: postgres `5433`, backend `8000`, frontend `5173`.

### Docker — production (from `edziennik/`)

```bash
docker compose -f compose.prod.yaml up -d --build
docker compose -f compose.prod.yaml logs -f
```

Port `80` → nginx (serves React SPA + proxies `/api/`, `/admin/`, `/static/`, `/docs/` to gunicorn).
Copy `edziennik/.env.prod.example` → `edziennik/.env` on the server before first run.

## Architecture

### Backend

Django project at `edziennik/edziennik/` with feature-based apps:

| App | Domain | Key endpoints |
|-----|--------|---------------|
| `users` | Students, teachers, parents, classes | `/api/uczniowie/`, `/api/nauczyciele/`, `/api/klasy/` |
| `grades` | Grades, subjects, period/final grades | `/api/oceny/`, `/api/przedmioty/` |
| `attendance` | Presence tracking | `/api/frekwencja/` |
| `timetables` | Schedules, lesson hours | `/api/plan-lekcji/` |
| `authentication` | JWT login/refresh | `/api/auth/` |
| `utils` | Lucky number, messages, events, homework | `/api/lucky-number/`, etc. |

All app routers are combined in `edziennik/api_router.py` and mounted at `/api/`. Authentication uses Simple JWT with custom handling in the `authentication` app.

### Frontend

React SPA at `edziennik-frontend/src/` with role-aware routing:

- **`services/api.ts`** — central API client; all fetch calls go through `fetchWithAuth()` which auto-refreshes JWT on 401
- **`services/auth.ts`** — JWT token lifecycle (storage, refresh, decode)
- **`hooks/useCurrentUser.ts`** — primary user context hook used throughout the app
- **`types/api.ts`** — shared TypeScript interfaces for all API responses
- **`constants.ts`** — API base URL, role constants

Components are organized by feature (`grades/`, `attendance/`, `timetable/`, `teacher/`, etc.) with shared UI primitives in `components/ui/`.

State management: TanStack React Query with a key factory in `services/queryKeys.ts`. Forms use React Hook Form + Zod.

### Data flow

```
Component → useQuery/useMutation → fetchWithAuth() → DRF endpoint
           ← React Query cache   ← JSON response   ← Django serializer
```

## Configuration

**Backend** — copy `edziennik/.env.example` → `edziennik/.env`:
- `SECRET-KEY`, `DEBUG`, `ALLOWED-HOSTS`
- `DATABASE_URL` — single postgres URL; takes priority over individual `DB_*` vars. Set this to the shared dev server URL when it is available. Leave empty → falls back to `DB_*` vars (Docker local postgres) or SQLite.
- `DB_NAME`, `DB_USER`, `DB_PASSWORD` — used by Docker local postgres when `DATABASE_URL` is empty.
- `CORS_ALLOW_ALL_ORIGINS=True` in dev, `False` in prod.

**Backend production** — copy `edziennik/.env.prod.example` → `edziennik/.env` on server.

**Frontend** — copy `edziennik-frontend/.env.example` → `edziennik-frontend/.env`:
- `VITE_API_BASE_URL` — `http://localhost:8001/api` for dev, `/api` in production (nginx handles routing).
- `VITE_FIREBASE_*` — Firebase credentials (push notifications).

### Database priority (settings.py)

`DATABASE_URL` → individual `DB_*` vars (postgres) → SQLite fallback (dev only).

## Documentation

- `edziennik/docs/` — API reference per domain (USERS.md, GRADES.md, etc.), testing guide, populate guide
- `edziennik-frontend/docs/` — feature specs, design system (light/dark/OLED themes)

## Agent Team & Workflow

Full agent definitions: `.claude/AGENTS.md`. Full workflow: `.claude/WORKFLOW.md`.

### When to spawn which agent

| Trigger | Agent |
|---------|-------|
| Nowy endpoint Django | `django-backend-architect` 🔴 |
| Nowy komponent React | `react-ui-developer` 🔵 |
| Docker / deploy / env | `devops-infra-manager` 🟢 |
| Testy po zmianie | `qa-test-engineer` 🟣 |
| Każda znacząca zmiana kodu | `code-review-guardian` 🩵 (proaktywnie) |
| Multi-agent / sprint planning | `team-orchestrator` 🟠 |
| Auth / RBAC / dane wrażliwe | `security-auditor` 🟡 |
| Nowe docs / endpoint bez doc | `tech-docs-writer` 🩵 |
| Propozycja: Redux / microservices / Redis | `critical-reviewer` 🩷 (przed implementacją) |
| Nowy ekran / komponent mobilny React Native | `react-native-mobile-dev` 📱 |

### Automatic triggers (proactive delegation)

- Po wygenerowaniu kodu → spawn `code-review-guardian`
- Po nowym endpoincie z danymi wrażliwymi → spawn `security-auditor`
- Po migracji DB → spawn `devops-infra-manager` (weryfikacja)
- Po złożonej propozycji architektonicznej → spawn `critical-reviewer`

### Escalate to user — NEVER do without asking

- Zmiany schematu DB (nowe modele, usuwanie kolumn)
- Zmiany JWT / systemu uprawnień
- Breaking changes API
- Nowe zależności pip / npm
- Zmiany Docker / zmiennych prod
- Push do remote / PR merge

### Spawning agents — required prompt prefix

Agents with `Caveman: tak` (django-backend-architect, react-ui-developer, devops-infra-manager, qa-test-engineer, code-review-guardian, react-native-mobile-dev) — add to prompt:
```
Start by running: /caveman
```

Agents with `Caveman: nie` (team-orchestrator, security-auditor, tech-docs-writer, critical-reviewer) — no prefix needed.
