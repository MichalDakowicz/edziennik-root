# Test spersonalizowanego powitania dla rodzica

## Co zostało zmienione:

### 1. **DashboardHome.tsx**
- Zawsze używa `user.firstName` (imię rodzica)
- Przekazuje `isParent={true}` i `activeChildName` do StudentDashboard

### 2. **StudentDashboard.tsx**
- Przyjmuje nowe propsy: `isParent` i `activeChildName`
- Przekazuje je do StudentGreeting

### 3. **StudentGreeting.tsx**
- Pokazuje imię rodzica w nagłówku
- Zmienia podtytuł na: "Panel rodzica - przeglądasz dane {imię dziecka}"

## Test:

### 1. Zaloguj się jako rodzic
- Username: `bianka_klosiewicz`
- Password: `bianka`

### 2. Sprawdź dashboard
Powinno być:
```
Dzień dobry, Bianka
Panel rodzica - przeglądasz dane Konstanty Matyjaszek
```

**NIE** powinno być:
```
Dzień dobry, Konstanty  ❌
```

### 3. Sprawdź inne strony
- Oceny → "Oceny Konstantego" (z odmianą)
- Frekwencja → "Frekwencja Konstantego"
- Zadania domowe → Read-only view

## Oczekiwany rezultat:
✅ Nagłówek pokazuje imię RODZICA (Bianka)
✅ Podtytuł informuje o przeglądaniu danych DZIECKA (Konstanty Matyjaszek)
✅ Jasne rozróżnienie między rodzicem a dzieckiem
✅ Spersonalizowany panel dla rodzica

## Dodatkowe informacje:
- Imię rodzica: `user.firstName` (z JWT tokena)
- Imię dziecka: `activeStudent.first_name` (z localStorage/API)
- Pełne imię dziecka: `${activeStudent.first_name} ${activeStudent.last_name}`
