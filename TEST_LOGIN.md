# Test logowania rodzica - Instrukcja

## Krok po kroku:

1. **Otwórz przeglądarkę w trybie incognito** (Cmd+Shift+N)
   - To zapewni czysty stan bez cache

2. **Otwórz DevTools** (Cmd+Option+I)
   - Przejdź do zakładki **Console**

3. **Wejdź na** http://localhost:5173

4. **Zaloguj się jako rodzic:**
   - Username: `bianka_klosiewicz`
   - Password: `bianka`

5. **Sprawdź w Console:**
   - Powinien być event `parent-children-updated`
   - Nie powinno być requestu do `/api/parent/students/`

6. **Sprawdź w Application tab:**
   - localStorage → `parent:children` powinien zawierać:
   ```json
   [{"id":193,"first_name":"Konstanty","last_name":"Matyjaszek","class_name":"A","class_id":9}]
   ```

7. **Sprawdź UI:**
   - Dziecko powinno być widoczne **NATYCHMIAST** bez Ctrl+R
   - Nie powinno być loadingu

## Oczekiwany rezultat:
✅ Dziecko ładuje się natychmiast po logowaniu
✅ Brak dodatkowego requestu do API
✅ Brak potrzeby odświeżania strony

## Jeśli nie działa:
1. Sprawdź Network tab - czy jest request do `/api/parent/students/`
2. Sprawdź Console - czy są błędy
3. Sprawdź localStorage - czy `parent:children` jest zapisane
