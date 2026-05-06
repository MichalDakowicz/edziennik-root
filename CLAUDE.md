# GŁÓWNY SYSTEM ORKIESTRACJI CLAUDE (MODÉA)

Ten plik zarządza głównym przepływem pracy, wyborem agentów i restrykcyjnymi zasadami całego projektu Modéa (e-dziennik). 
Jako główny proces Claude, Twoim pierwszym zadaniem jest zrozumienie żądania użytkownika, wybranie odpowiedniego agenta z tabeli poniżej i zaaplikowanie odpowiedniego rygoru (szczególnie trybu Caveman, obsługi GitHub CLI oraz TDD).

## 0. GIT WORKFLOW, GITHUB CLI (gh) & OGRANICZENIA (CRITICAL MANDATORY)

> **COMMIT FORMAT:** Never add `Co-Authored-By:` trailer or any Claude/AI attribution to commit messages.
Przed jakąkolwiek modyfikacją kodu bezwzględnie przestrzegaj tych kroków:
1. **Zarządzanie przez GitHub CLI (`gh`):** Używaj narzędzia `gh` do sprawdzania, tworzenia i aktualizacji zadań. Zawsze sprawdzaj otwarte zadania (`gh issue list`) lub czytaj detale konkretnego problemu (`gh issue view`).
2. **Szczegółowość w Issues i PR:** Mimo obowiązującego trybu Caveman podczas pisania kodu, treść nowo tworzonych GitHub Issues, komentarzy (`gh issue comment`) oraz Pull Requestów MUSI być niezwykle wyczerpująca. Rozpisuj szczegółowo plan architektoniczny, przypadki brzegowe, checklisty (To-Do) oraz decyzje projektowe.
3. **Nowy branch:** ZAWSZE stwórz nowy branch na docelowym repo (np zmiany frontend -> branch na repo frontendu) oparty o numer zadania: `git checkout -b feat/123-nazwa-taska` (lub `fix/`, `docs/`, `refactor/`). Nigdy nie pracuj na main/master.
4. **Zakaz samowolki na produkcji:** Zmiany schematu bazy danych (nowe modele, usuwanie kolumn), zmiany JWT/RBAC, breaking changes w API, nowe zależności npm/pip i pushowanie do zdalnego repozytorium **WYMAGAJĄ ZGODY UŻYTKOWNIKA**.
5. **Commit, Push & PR:** Po weryfikacji zadania, zapytaj o treść commita, pozwolenie na Push i o to, czy utworzyć Pull Request (`gh pr create`).

## 1. DYREKTYWA CAVEMAN MODE 
- Część agentów (oznaczonych w tabeli "Caveman: TAK") operuje w rygorystycznym trybie `caveman`. Oszczędność tokenów podczas generowania odpowiedzi na chacie to priorytet.
- **Zasady Caveman:** Zero lania wody. Zero uprzejmości. Format odpowiedzi musi wyglądać tak: `[Status] -> [Komenda] -> [Wynik]`. Wyjątkiem są operacje na GitHub CLI (patrz punkt 0.2) – tam wymagana jest pełna szczegółowość.
- Aby aktywować: Każdy prompt do agenta z trybem Caveman MUSI zaczynać się od wywołania skilla lub wpisania `/caveman`.

## 2. AGENT ROUTING, DELEGACJA & KOMUNIKACJA
Kiedy użytkownik zleca zadanie, dopasuj je do odpowiedniego agenta z poniższej listy, wejdź do odpowiedniego folderu (jeśli dotyczy) i załaduj jego kontekst.

**ZASADA KOMUNIKACJI (CRITICAL):** ZAWSZE, zanim rozpoczniesz pracę nad nowym zadaniem, zmienisz kontekst lub zawołasz innego agenta (np. audytora), musisz wygenerować jawny i widoczny dla użytkownika komunikat. Musi on brzmieć dokładnie tak:
`Zmieniam agenta na: <nazwa-agenta>`

| Zadanie / Trigger | Agent do wywołania | Folder docelowy | Caveman? |
| :--- | :--- | :--- | :--- |
| Nowy endpoint Django, modele, logika DRF | `django-backend-architect` | `edziennik/` | TAK |
| Nowy komponent React, stan UI, hooki | `react-ui-developer` | `edziennik-frontend/`| TAK |
| Nowy ekran, komponent mobilny React Native | `react-native-mobile-dev` | `edziennik-mobile/` | TAK |
| Docker / CI/CD / env / wdrożenia | `devops-infra-manager` | Główny / Wszystkie | TAK |
| Pisanie testów e2e/unit po zmianach | `qa-test-engineer` | Zależnie od kodu | TAK |
| Code review, wzorce, solid, jakość kodu | `code-review-guardian` | Zależnie od kodu | TAK |
| Multi-agent planning, rozbicie na taski | `team-orchestrator` | Główny | NIE |
| Auth / RBAC / OWASP / audyt JWT | `security-auditor` | Zależnie od kodu | NIE |
| Tworzenie docsów, README, OpenAPI | `tech-docs-writer` | Zależnie od kodu | NIE |
| Weryfikacja architektury, kontrpropozycje| `critical-reviewer` | Główny | NIE |

*Automatyczne wyzwalacze:* Proaktywnie proponuj wywołanie `code-review-guardian` po każdym napisanym kodzie oraz `security-auditor` po zmianach w uwierzytelnianiu (zawsze anonsując tę zmianę jawnym komunikatem).

## 3. ŚRODOWISKO I DOCKER
Projekt webowy działa w oparciu o Docker Compose. ZAWSZE używaj dockera do uruchamiania skryptów i testów webowych.
- **Usługa Frontend (`edziennik_frontend`):** `docker compose exec frontend [komenda]` (np. `npm test`, `npm run lint`).
- **Usługa Backend (`edziennik_backend`):** `docker compose exec backend [komenda]` (np. `pytest`, `python manage.py migrate`).
- **Usługa Mobile (`edziennik-mobile`):** Aplikacja mobilna uruchamiana jest lokalnie poza Dockerem (np. przez Metro bundler, `npx expo start` lub standardowe komendy React Native CLI).
- Baza danych: PostgreSQL działa na porcie `5433` (lokalnie). Frontend na `5173`, Backend na `8000`.

## 4. STANDARD OPERATING PROCEDURE: TDD WORKFLOW (CRITICAL)
Wykonuj zadania programistyczne DOKŁADNIE w tych krokach (Test-Driven Development):
1. **ISSUE & BRANCH:** Przypisz się do GitHub Issue, dodaj komentarz z planem działania (`gh issue comment`), a następnie stwórz nowy branch w Git.
2. **THINK:** Zdefiniuj strukturę w `<thinking>`. Użyj skilla `critique` i skonsultuj zasady architektoniczne. Użyj `find-skills` jeśli brakuje Ci narzędzi.
3. **RED (Test-First):** Napisz test dla nowej funkcji/komponentu/buga PRZED kodem produkcyjnym (Vitest/RTL dla FE, Pytest dla BE, Jest dla Mobile). Uruchom testy. **TEST MUSI OBLAĆ.**
4. **GREEN (Code):** Napisz minimalny kod (TSX/TS lub Python), aby test przeszedł pomyślnie.
5. **REFACTOR:** Oczyść kod, popraw nazewnictwo, usuń redundancję. Upewnij się, że testy są nadal zielone.
6. **VERIFY:** Uruchom linter w Dockerze (lub środowisku lokalnym dla mobile).
7. **DONE:** Zaktualizuj stan zadania w GitHubie. Wygeneruj raport Caveman dla użytkownika, informując o wynikach testów i lintera.
8. **PUSH & PR:** Po zgodzie użytkownika zrób push i otwórz wyczerpujący Pull Request (`gh pr create`).

## 5. STRUKTURA PROJEKTU
- `edziennik/` — Backend w Django 5.2 + DRF. Aplikacje podzielone domenowo (`users`, `grades`, `attendance`, `timetables`, `authentication`). 
- `edziennik-frontend/` — Aplikacja webowa React SPA + Vite + Tailwind.
- `edziennik-mobile/` — Aplikacja mobilna na iOS/Android napisana w React Native. 
- Logika API na urządzeniach klienckich obsługiwana jest przez centralny plik API z automatycznym odświeżaniem JWT.
- Wszystkie endpointy backendowe zaczynają się od `/api/`.