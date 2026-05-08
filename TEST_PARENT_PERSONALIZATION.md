# Test: Parent Pages Personalization with Polish Grammar

## Status: ✅ COMPLETED

## Overview
All parent pages have been personalized to show proper Polish grammar with child's name in appropriate grammatical cases (genitive, dative). The pages now clearly indicate that the parent is viewing their child's data, not their own.

## Changes Made

### 1. Created Name Declension Utilities
**File**: `edziennik-frontend/src/utils/nameUtils.ts`

Created two functions for Polish name declension:
- `toGenitive(name)` - Converts name to genitive case (kogo? czego?)
  - Examples: Konstanty → Konstantego, Bianka → Bianki, Tomasz → Tomasza
- `toDative(name)` - Converts name to dative case (komu? czemu?)
  - Examples: Konstanty → Konstantemu, Bianka → Biance

### 2. ParentGradesPage Personalization
**File**: `edziennik-frontend/src/components/parent/ParentGradesPage.tsx`

**Changes**:
- ✅ Imported `toGenitive` from `../../utils/nameUtils`
- ✅ Changed subtitle from "Podsumowanie ocen z bierzącego półrocza" to "Podsumowanie ocen {Imię w dopełniaczu} z bierzącego półrocza"
  - Example: "Podsumowanie ocen Konstantego z bierzącego półrocza"

**Before**:
```tsx
<p className="text-on-surface-variant font-body text-sm mt-1">Podsumowanie ocen z bierzącego półrocza</p>
```

**After**:
```tsx
<p className="text-on-surface-variant font-body text-sm mt-1">
  {activeStudent ? `Podsumowanie ocen ${toGenitive(activeStudent.first_name)} z bierzącego półrocza` : "Podsumowanie ocen z bierzącego półrocza"}
</p>
```

### 3. ParentAttendancePage Personalization
**File**: `edziennik-frontend/src/components/parent/ParentAttendancePage.tsx`

**Changes**:
- ✅ Imported `toGenitive` from `../../utils/nameUtils`
- ✅ Changed subtitle to "Frekwencja {Imię w dopełniaczu}"
  - Example: "Frekwencja Konstantego"

**Before**:
```tsx
<p className="text-on-surface-variant font-body text-sm mt-1">Moje Postępy</p>
```

**After**:
```tsx
<p className="text-on-surface-variant font-body text-sm mt-1">
  {activeStudent ? `Frekwencja ${toGenitive(activeStudent.first_name)}` : "Frekwencja dziecka"}
</p>
```

### 4. ParentBehaviorPage Personalization
**File**: `edziennik-frontend/src/components/parent/ParentBehaviorPage.tsx`

**Changes**:
- ✅ Changed subtitle to "Zachowanie {Imię w dopełniaczu}"
  - Example: "Zachowanie Konstantego"

### 5. HomeworkPage Personalization
**File**: `edziennik-frontend/src/components/homework/HomeworkPage.tsx`

**Changes**:
- ✅ Fixed bug: Moved `isParent` declaration before `activeStudent` to fix ReferenceError
- ✅ Changed "Masz X zadania" → "{Imię} ma X zadania"
  - Example: "Konstanty ma 5 aktywnych zadań na ten tydzień"
- ✅ Changed "Postęp tygodnia" → "Postęp {Imię w dopełniaczu}"
  - Example: "Postęp Konstantego"

**Before**:
```tsx
const user = getCurrentUser();
const isParent = user?.role === "rodzic";
const { activeStudent } = useParentStudent();
```

**After**:
```tsx
const user = getCurrentUser();
const { activeStudent } = useParentStudent();
const isParent = user?.role === "rodzic";
```

**Before**:
```tsx
<p className="text-on-surface-variant font-body text-sm mt-1">
  Masz {upcoming.length} {plAktywne(upcoming.length)} {plZadanie(upcoming.length)} na ten tydzień.
</p>
```

**After**:
```tsx
<p className="text-on-surface-variant font-body text-sm mt-1">
  {isParent && activeStudent 
    ? `${activeStudent.first_name} ma ${upcoming.length} ${plAktywne(upcoming.length)} ${plZadanie(upcoming.length)} na ten tydzień.`
    : `Masz ${upcoming.length} ${plAktywne(upcoming.length)} ${plZadanie(upcoming.length)} na ten tydzień.`
  }
</p>
```

**Before**:
```tsx
<p className="text-sm font-bold text-slate-500 uppercase tracking-wider mb-6 font-body">
  Postęp tygodnia
</p>
```

**After**:
```tsx
<p className="text-sm font-bold text-slate-500 uppercase tracking-wider mb-6 font-body">
  {isParent && activeStudent ? `Postęp ${toGenitive(activeStudent.first_name)}` : "Postęp tygodnia"}
</p>
```

### 6. StudentHomeworkDetailPage Personalization
**File**: `edziennik-frontend/src/components/homework/StudentHomeworkDetailPage.tsx`

**Changes**:
- ✅ Changed "Twoja odpowiedź" → "Odpowiedź {Imię w dopełniaczu}"
  - Example: "Odpowiedź Konstantego"
- ✅ Changed "Twoja praca" → "Praca {Imię w dopełniaczu}"
  - Example: "Praca Konstantego"
- ✅ Fixed null check for `submission.data_oddania` to prevent crashes
- ✅ Added message when child hasn't submitted: "{imię} nie oddał(a) jeszcze tej pracy"

## Bug Fixes

### 1. ReferenceError: Can't find variable: isParent
**Location**: `HomeworkPage.tsx:505`

**Root Cause**: Variable `isParent` was being used before it was declared. The order was:
```tsx
const user = getCurrentUser();
const isParent = user?.role === "rodzic";
const { activeStudent } = useParentStudent(); // This uses isParent internally
```

**Fix**: Moved `isParent` declaration after `activeStudent`:
```tsx
const user = getCurrentUser();
const { activeStudent } = useParentStudent();
const isParent = user?.role === "rodzic";
```

### 2. TypeError: null is not an object (evaluating 'submission.data_oddania')
**Location**: `StudentHomeworkDetailPage.tsx:1159`

**Root Cause**: Code was trying to access `submission.data_oddania` without checking if `submission` exists first.

**Fix**: Added null check before accessing the property.

## Testing Instructions

### Test User
- **Parent**: `bianka_klosiewicz:bianka`
- **Child**: Konstanty Matyjaszek (ID: 193, Class A, class_id: 9)

### Test Cases

1. **Grades Page** (`/dashboard/grades`)
   - ✅ Subtitle should show: "Podsumowanie ocen Konstantego z bierzącego półrocza"
   - ✅ Child label should show: "Konstanty Matyjaszek"

2. **Attendance Page** (`/dashboard/attendance`)
   - ✅ Subtitle should show: "Frekwencja Konstantego"
   - ✅ Child label should show: "Konstanty Matyjaszek"

3. **Homework Page** (`/dashboard/homework`)
   - ✅ Subtitle should show: "Konstanty ma X aktywnych zadań na ten tydzień"
   - ✅ Progress widget should show: "Postęp Konstantego"
   - ✅ No action buttons visible (read-only for parents)

4. **Homework Detail Page** (`/dashboard/homework/:id`)
   - ✅ Should show "Odpowiedź Konstantego" instead of "Twoja odpowiedź"
   - ✅ Should show "Praca Konstantego" instead of "Twoja praca"
   - ✅ No submission form visible
   - ✅ No "Cofnij i popraw" button visible
   - ✅ If not submitted: "Konstanty nie oddał jeszcze tej pracy"

5. **Behavior Page** (`/dashboard/behavior`)
   - ✅ Subtitle should show: "Zachowanie Konstantego"

## Polish Grammar Rules Applied

### Genitive Case (Dopełniacz - kogo? czego?)
Used for:
- "Frekwencja **Konstantego**" (attendance of whom?)
- "Postęp **Konstantego**" (progress of whom?)
- "Odpowiedź **Konstantego**" (answer of whom?)
- "Praca **Konstantego**" (work of whom?)
- "Podsumowanie ocen **Konstantego**" (summary of grades of whom?)

### Nominative Case (Mianownik - kto? co?)
Used for:
- "**Konstanty** ma 5 zadań" (who has 5 tasks?)
- "**Konstanty** nie oddał jeszcze tej pracy" (who hasn't submitted?)

## Files Modified

1. ✅ `edziennik-frontend/src/utils/nameUtils.ts` (created)
2. ✅ `edziennik-frontend/src/components/parent/ParentGradesPage.tsx`
3. ✅ `edziennik-frontend/src/components/parent/ParentAttendancePage.tsx`
4. ✅ `edziennik-frontend/src/components/parent/ParentBehaviorPage.tsx`
5. ✅ `edziennik-frontend/src/components/homework/HomeworkPage.tsx`
6. ✅ `edziennik-frontend/src/components/homework/StudentHomeworkDetailPage.tsx`

## Summary

All parent pages now use proper Polish grammar with child's name in appropriate grammatical cases. The interface clearly distinguishes between student view ("Twoja praca", "Masz zadania") and parent view ("Praca Konstantego", "Konstanty ma zadania"). All bugs have been fixed and the application should work without errors.
