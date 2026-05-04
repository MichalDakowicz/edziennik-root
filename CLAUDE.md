# GŁÓWNY SYSTEM ORKIESTRACJI CLAUDE (MODÉA)

Ten plik zarządza głównym przepływem pracy, wyborem agentów i restrykcyjnymi zasadami całego projektu Modéa (e-dziennik). 
Jako główny proces Claude, Twoim pierwszym zadaniem jest zrozumienie żądania użytkownika, wybranie odpowiedniego agenta z tabeli poniżej i zaaplikowanie odpowiedniego rygoru (szczególnie trybu Caveman i TDD).

## 0. GIT WORKFLOW & OGRANICZENIA (CRITICAL MANDATORY)
Przed jakąkolwiek modyfikacją kodu bezwzględnie przestrzegaj tych kroków:
1. **ZAWSZE stwórz nowy branch:** `git checkout -b feat/nazwa-taska` (lub `fix/`, `docs/`, `refactor/`). Nigdy nie pracuj na main/master.
2. **Zakaz samowolki na produkcji:** Zmiany schematu bazy danych (nowe modele, usuwanie kolumn), zmiany JWT/RBAC, breaking changes w API, nowe zależności npm/pip i pushowanie do zdalnego repozytorium **WYMAGAJĄ ZGODY UŻYTKOWNIKA**.
3. **Commit & Push:** Po weryfikacji zadania w Dockerze, zawsze zapytaj o treść commita i pozwolenie na Push.

## 1. DYREKTYWA CAVEMAN MODE 
- Część agentów (oznaczonych w tabeli "Caveman: TAK") operuje w rygorystycznym trybie `caveman`. Oszczędność tokenów to priorytet.
- **Zasady Caveman:** Zero lania wody. Zero uprzejmości. Format odpowiedzi musi wyglądać tak: `[Status] -> [Komenda] -> [Wynik]`.
- Aby aktywować: Każdy prompt do agenta z trybem Caveman MUSI zaczynać się od wywołania skilla lub wpisania `/caveman`.

## 2. AGENT ROUTING & DELEGACJA
Kiedy użytkownik zleca zadanie, dopasuj je do odpowiedniego agenta z poniższej listy, wejdź do odpowiedniego folderu (jeśli dotyczy) i załaduj jego kontekst.

| Zadanie / Trigger | Agent do wywołania | Folder docelowy | Caveman? |
| :--- | :--- | :--- | :--- |
| Nowy endpoint Django, modele, logika DRF | `django-backend-architect` | `edziennik/` |**TAK**|
| Nowy komponent React, stan UI, hooki | `react-ui-developer` | `edziennik-frontend/`|**TAK**|
| Docker / CI/CD / env / wdrożenia | `devops-infra-manager` | Główny / Oba |**TAK**|
| Pisanie testów e2e/unit po zmianach | `qa-test-engineer` | Zależnie od kodu |**TAK**|
| Code review, wzorce, solid, jakość kodu | `code-review-guardian` | Zależnie od kodu |**TAK**|
| Multi-agent planning, rozbicie na taski | `team-orchestrator` | Główny |NIE|
| Auth / RBAC / OWASP / audyt JWT | `security-auditor` | Zależnie od kodu |NIE|
| Tworzenie docsów, README, OpenAPI | `tech-docs-writer` | Zależnie od kodu |NIE|
| Weryfikacja architektury, kontrpropozycje| `critical-reviewer` | Główny |NIE|

*Automatyczne wyzwalacze:* Proaktywnie proponuj wywołanie `code-review-guardian` po każdym napisanym kodzie oraz `security-auditor` po zmianach w uwierzytelnianiu.

## 3. ŚRODOWISKO I DOCKER
Projekt działa w oparciu o Docker Compose. ZAWSZE używaj dockera do uruchamiania skryptów i testów. Nie uruchamiaj ich lokalnie.
- **Usługa Frontend (`edziennik_frontend`):** `docker compose exec frontend [komenda]` (np. `npm test`, `npm run lint`).
- **Usługa Backend (`edziennik_backend`):** `docker compose exec backend [komenda]` (np. `pytest`, `python manage.py migrate`).
- Baza danych: PostgreSQL działa na porcie `5433` (lokalnie). Frontend na `5173`, Backend na `8000`.

## 4. STANDARD OPERATING PROCEDURE: TDD WORKFLOW (CRITICAL)
Wykonuj zadania programistyczne DOKŁADNIE w tych krokach (Test-Driven Development):
1. **BRANCH:** Stwórz nowy branch w Git.
2. **THINK:** Zdefiniuj strukturę i plan w `<thinking>`. Użyj skilla `critique` i skonsultuj zasady (np. Vercel dla Reacta, clean-architecture dla Django). Użyj `find-skills` jeśli brakuje Ci narzędzi.
3. **RED (Test-First):** Napisz test dla nowej funkcji/komponentu/buga PRZED kodem produkcyjnym (Vitest dla FE, Pytest/Django dla BE). Uruchom w Dockerze. **TEST MUSI OBLAĆ.**
4. **GREEN (Code):** Napisz minimalny kod (TSX/TS lub Python), aby test przeszedł pomyślnie.
5. **REFACTOR:** Oczyść kod, popraw nazewnictwo, usuń redundancję. Upewnij się, że testy są nadal "zielone".
6. **VERIFY:** Uruchom linter (np. `flake8` lub `eslint` i kompilator TS) w Dockerze.
7. **DONE:** Wygeneruj raport (w stylu Cavemana jeśli dotyczy) i info o branchu. Koniecznie wspomnij o wynikach testów i lintera.
8. **PUSH:** Zapytaj użytkownika o zgodę na zrobienie commit i push.

## 5. STRUKTURA PROJEKTU
- `edziennik/` — Backend w Django 5.2 + DRF. Aplikacje podzielone domenowo (`users`, `grades`, `attendance`, `timetables`, `authentication`). 
- `edziennik-frontend/` — React SPA + Vite + Tailwind. Logika API w `services/api.ts` (fetchWithAuth dla JWT), stan obsługiwany przez TanStack React Query.
- Wszystkie endpointy backendowe zaczynają się od `/api/`.