# Modéa — Project Workflow

## Trigger points

### Nowa funkcjonalność (feature)
```
user → team-orchestrator
         ├── django-backend-architect   (model, serializer, endpoint)
         │     └── [done] → code-review-guardian
         ├── react-ui-developer         (komponent, hook, integracja API)
         │     └── [done] → code-review-guardian
         ├── qa-test-engineer           (testy backend + frontend)
         └── tech-docs-writer           (aktualizacja docs/)
```

### Bugfix
```
user → django-backend-architect | react-ui-developer
         └── [fix done] → qa-test-engineer (regression test)
                            └── code-review-guardian
```

### Złożone zadanie cross-stack
```
user → team-orchestrator
         ├── analizuje zależności
         ├── deleguje równolegle: backend + frontend
         ├── blokuje: testy czekają na oba
         └── eskaluje do user: zmiany DB, breaking API, nowe zależności
```

### Przed wdrożeniem produkcyjnym
```
user → security-auditor        (pełny audit: JWT, RBAC, OWASP)
         └── [raport] → user
user → devops-infra-manager    (build, migrate, deploy, verify)
         └── [monitoring] → devops-infra-manager (health check)
```

### Propozycja architektoniczna
```
user/agent → critical-reviewer
               ├── ocenia złożoność vs. istniejący stack
               ├── APPROVE / REWORK / REJECT
               └── [approved] → team-orchestrator (delegacja implementacji)
```

---

## Reguły delegowania

| Sytuacja | Agent |
|----------|-------|
| Nowy endpoint Django | `django-backend-architect` |
| Nowy komponent React | `react-ui-developer` |
| Docker / deploy / env | `devops-infra-manager` |
| Testy po zmianie | `qa-test-engineer` |
| Każda znacząca zmiana kodu | `code-review-guardian` (proaktywnie) |
| Multi-agent / sprint | `team-orchestrator` |
| Nowy auth / RBAC / dane wrażliwe | `security-auditor` |
| Nowe docs / endpoint bez doc | `tech-docs-writer` |
| Redux/microservices/Redis proposal | `critical-reviewer` |

---

## Automatyczne wyzwalacze

- Po wygenerowaniu kodu → `code-review-guardian` (proaktywnie)
- Po nowym endpoincie z danymi wrażliwymi → `security-auditor`
- Po migracji DB → `devops-infra-manager` (weryfikacja entrypoint)
- Po złożonej propozycji → `critical-reviewer` (przed implementacją)

---

## Eskalacja do usera (nie rób bez pytania)

- Zmiany schematu DB (nowe modele, usuwanie kolumn)
- Zmiany JWT / systemu uprawnień
- Breaking changes API
- Nowe zależności pip / npm
- Zmiany Docker / zmiennych środowiskowych prod
- Push do remote / PR merge

---

## Kolejność faz dla dużego feature

```
Faza 1 — Analiza (team-orchestrator)
  → dekompozycja zadań, identyfikacja zależności, plan

Faza 2 — Backend (django-backend-architect)
  → model → migration → serializer → view → URL → testy jednostkowe

Faza 3 — Frontend (react-ui-developer)  [równolegle z fazą 2 jeśli możliwe]
  → types/api.ts → services/api.ts → queryKeys → komponent → testy

Faza 4 — QA (qa-test-engineer)
  → scenariusze testowe → testy integracyjne → bug report jeśli błędy

Faza 5 — Review (code-review-guardian)
  → security check → pattern compliance → approval

Faza 6 — Docs (tech-docs-writer)
  → edziennik/docs/ lub edziennik-frontend/docs/

Faza 7 — Deploy (devops-infra-manager)
  → build → migrate → deploy → health check
```
