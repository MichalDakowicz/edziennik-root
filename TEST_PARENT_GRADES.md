# Test strony ocen dla rodzica

## Co zostało zrobione:

### Backend (`edziennik/parent/api/views.py`):
- Utworzono endpoint `/api/parent/dashboard/`
- Przyjmuje parametr `student_id` w query string
- Zwraca:
  ```json
  {
    "grades": [
      {
        "subject_id": 32,
        "subject_name": "WF",
        "average": null,
        "predicted": null,
        "final": "4.2",
        "period": [{"id": 4051, "wartosc": "4.2", "okres": 1}],
        "grades": [
          {
            "id": 25347,
            "wartosc": "4.5",
            "waga": 3,
            "opis": "",
            "data_wystawienia": "2026-05-06T09:34:26",
            "czy_do_sredniej": true
          }
        ]
      }
    ],
    "behavior": [
      {
        "id": 123,
        "punkty": 5,
        "opis": "Aktywność na lekcji",
        "data_wpisu": "2026-05-06",
        "teacher_name": "Jan Kowalski"
      }
    ]
  }
  ```

### Backend (`edziennik/parent/api/urls.py`):
- Dodano route `dashboard/` → `parent_dashboard_view`

### Frontend:
- `ParentGradesPage.tsx` już używa `getParentDashboard()`
- Endpoint jest zgodny z oczekiwaniami frontendu

## Test:

### 1. Zaloguj się jako rodzic
- Username: `bianka_klosiewicz`
- Password: `bianka`

### 2. Przejdź do Oceny
- Kliknij "Oceny" w menu bocznym
- Lub wejdź na: http://localhost:5173/dashboard/parent/grades

### 3. Sprawdź co powinno być widoczne:
✅ Nagłówek: "Oceny"
✅ Podtytuł: "Konstanty Matyjaszek" (imię dziecka)
✅ Średnia ogólna (np. 4.23)
✅ Punkty zachowania (suma)
✅ Lista przedmiotów z ocenami
✅ Zakładki: "Oceny cząstkowe", "Oceny okresowe", "Zachowanie"
✅ Analiza trendu (rosnące/spadające przedmioty)
✅ Podsumowanie końcowe

### 4. Sprawdź Network tab:
- Request: `GET /api/parent/dashboard/?student_id=193`
- Response: JSON z grades i behavior
- Status: 200 OK

### 5. Sprawdź funkcjonalność:
- Filtrowanie przedmiotów (search box)
- Przełączanie zakładek
- Kliknięcie na przedmiot → przekierowanie do szczegółów
- Filtr "Ostatni tydzień"

## Oczekiwany rezultat:
✅ Strona ocen ładuje się bez błędów
✅ Widoczne są wszystkie oceny dziecka
✅ Średnia jest poprawnie obliczona
✅ Zachowanie jest wyświetlone
✅ Wszystkie zakładki działają
✅ Brak błędów 404 w konsoli

## Dodatkowe endpointy (już istniejące):
- `/api/parent/students/` - lista dzieci
- `/api/parent/dashboard/?student_id=X` - oceny + zachowanie (NOWY)

## Jeśli nie działa:
1. Sprawdź Console - czy są błędy
2. Sprawdź Network - czy request do `/api/parent/dashboard/` zwraca 200
3. Sprawdź czy `activeStudentId` jest ustawione (DevTools → Components → ParentStudentContext)
4. Sprawdź backend logs: `docker-compose -f edziennik/compose.yaml logs backend | tail -50`
