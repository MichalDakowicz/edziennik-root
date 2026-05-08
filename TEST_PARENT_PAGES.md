# TEST: Parent Pages Connection

## Status: ✅ DONE

## Summary
Connected all parent pages (attendance, behavior, homework) to backend endpoints with full subject information.

## Changes Made

### 1. Backend: Created `/api/parent/behavior/` endpoint
**File:** `edziennik/parent/api/views.py`
- Added `parent_behavior_view` function
- Returns behavior points for specific student
- Verifies parent has access to requested student
- Format: `[{ id, punkty, opis, data_wpisu, teacher_name }]`

### 2. Backend: Created `/api/parent/attendance/` endpoint
**File:** `edziennik/parent/api/views.py`
- Added `parent_attendance_view` function
- Returns attendance WITH subject names (no frontend mapping needed)
- Maps attendance to subjects via timetable plan
- Format: `[{ id, Data, uczen, godzina_lekcyjna, status, status_name, subject_name }]`
- Supports date filtering: `date_from`, `date_to`

**File:** `edziennik/parent/api/urls.py`
- Added routes:
  - `path('behavior/', views.parent_behavior_view)`
  - `path('attendance/', views.parent_attendance_view)`
  - `path('submissions/', views.parent_homework_submissions_view)`

### 3. Backend: Created `/api/parent/submissions/` endpoint
**File:** `edziennik/parent/api/views.py`
- Added `parent_homework_submissions_view` function
- Returns homework submissions for specific student (child)
- Format: `[{ id, homework, uczen, tresc, zalacznik, data_oddania, status, ocena, komentarz }]`
- Allows parent to see what their child submitted

### 3. Frontend: Updated attendance page
**File:** `edziennik-frontend/src/services/api.ts`
- Added `getParentAttendance()` function
- Returns attendance with subject_name and status_name

**File:** `edziennik-frontend/src/components/parent/ParentAttendancePage.tsx`
- Changed to use `getParentAttendance()` instead of `getAttendance()`
- Simplified `recordToSubjectMap` - now just reads `subject_name` from API
- Updated `resolveStatusName` to use `status_name` from API
- No longer needs to fetch: subjects, zajecia, days, timetable (all done server-side)

### 5. Frontend: Updated homework page for parents (READ-ONLY with child's submissions)
**File:** `edziennik-frontend/src/components/homework/HomeworkPage.tsx`
- Added `useParentStudent()` hook
- Changed `classId` logic: uses `activeStudent.class_id` for parents
- **Parent sees child's submissions:**
  - Uses `/api/parent/submissions/?student_id={id}` endpoint
  - Shows submission status badges (ODDANE, SPRAWDZONE, ODRZUCONE, NIEODDANE)
  - No action buttons (read-only view)
- **Hidden for parents:**
  - "Oddaj zadanie" button
  - "Cofnij i popraw" button
  - "Oddaj ponownie" button
  - "Dodaj własne zadanie" button
  - SubmitHomeworkModal
- Parents can view homework and see what their child submitted
- Now works for both students (full access) and parents (read-only)

### 6. Frontend: Customized homework detail page for parents
**File:** `edziennik-frontend/src/components/homework/StudentHomeworkDetailPage.tsx`
- Added `useParentStudent()` hook
- **Personalized texts for parents:**
  - "Twoja odpowiedź" → "Odpowiedź {imię dziecka}"
  - "Twoja praca" → "Praca {imię dziecka}"
- **Hidden for parents:**
  - "Cofnij i popraw" button
  - Submission form (file upload, textarea, submit button)
  - Private comments section (student-teacher communication)
  - Public comment form (can read but not post)
- **Added for parents:**
  - Message when child hasn't submitted: "{imię} nie oddał(a) jeszcze tej pracy"
- **Fixed bug:** Added null check for `submission.data_oddania` to prevent crashes
- Parents can view:
  - Homework details and attachments
  - Child's submission (if submitted)
  - Public comments (class discussion) - read only
  - Grades and teacher feedback
- Parents cannot:
  - Submit work for child
  - Resubmit or edit child's work
  - Access private student-teacher comments
  - Post public comments

### 7. Frontend: Personalized homework list page for parents
**File:** `edziennik-frontend/src/components/homework/HomeworkPage.tsx`
- **Personalized texts for parents:**
  - "Masz X aktywne zadania" → "{imię dziecka} ma X aktywne zadania"
  - "Postęp tygodnia" → "Postęp {imię w dopełniaczu}" (np. "Postęp Konstantego")
- All statistics and progress bars show child's data
- Submission badges show child's submission status

### 8. Utility: Polish name declension
**File:** `edziennik-frontend/src/utils/nameUtils.ts`
- Created `toGenitive()` function for genitive case (kogo? czego?)
- Created `toDative()` function for dative case (komu? czemu?)
- **Examples:**
  - Konstanty → Konstantego (genitive)
  - Bianka → Bianki (genitive)
  - Tomasz → Tomasza (genitive)
  - Anna → Anny (genitive)
- Handles special cases: -ek, -eł, -ia, -ja endings
- Used in: "Odpowiedź Konstantego", "Praca Bianki", "Postęp Tomasza"

## Test Results

### Behavior Endpoint Test ✅
```bash
curl "http://localhost:8000/api/parent/behavior/?student_id=193"
```
Response: Array of behavior points with teacher names

### Attendance Endpoint Test ✅
```bash
curl "http://localhost:8000/api/parent/attendance/?student_id=193"
```
Response:
```json
{
  "id": 100813,
  "Data": "2026-05-06",
  "uczen": 193,
  "godzina_lekcyjna": 22,
  "status": 11,
  "status_name": "Obecny",
  "subject_name": "Historia"
}
```

### Homework Endpoint Test ✅
```bash
curl "http://localhost:8000/api/prace-domowe/?klasa=9"
```
Response: Array of homework assignments (existing endpoint works)

## User Credentials
- **Parent:** bianka_klosiewicz:bianka
- **Child ID:** 193 (Konstanty Matyjaszek, Class A, class_id: 9)

## Pages to Test
1. `/dashboard/parent/attendance` - Frekwencja ✅ (with subject names)
2. `/dashboard/parent/behavior` - Zachowanie ✅
3. `/dashboard/homework` - Zadania domowe ✅ (uses child's class)

## Key Improvements
- **Attendance now shows subject names** - no more empty subject columns
- **Server-side mapping** - faster, more reliable than client-side
- **Homework works for parents** - automatically uses active child's class
- **Proper Polish grammar** - names are declined correctly (Konstanty → Konstantego)
- All endpoints verify parent access to student
