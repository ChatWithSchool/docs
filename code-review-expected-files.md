# הכנה לקוד ריוויו: אילו קבצים אמורים להשתנות

המקורות: `school-client/docs/build-a-screen.md`, `school-server/docs/sprint-2-schemas.md`, ו-`school-docs/spec/tasks.html`.

---

## משימת הקליינט (ספרינט 1), בריפו `school-client`

ענף: `feature/<מפתח>-<מספר>-<מסך>-client`, מתוך `dev`.

### קבצים שאמורים להשתנות

| קובץ | מה צריך להיות בו |
|---|---|
| `src/screens/<מסך>/index.tsx` | ה-placeholder הוחלף במסך עצמו: `PageHeader`, מצבי מסך מתוך `components/StateViews.tsx`, ותוכן שנבנה מהרכיבים ב-`components/ui` |
| `src/api/<משאב>Api.ts` (קובץ חדש) | `apiSlice.injectEndpoints`, באותו דפוס של `announcementsApi.ts` |
| `src/mocks/handlers.ts` | handlers לפי `openapi/openapi.yaml`, רק לאוספים הפרטיים של המסך |
| `src/mocks/shared/...` (לפעמים) | רק כשהמסך כותב לאוסף משותף: שינוי בנתונים, כדי שה-GET יחזיר את מה שנכתב. אסור להוסיף שם GET נוסף |

### קבצים שאסור שישתנו בלי אישור של ראשת צוות

- `src/api/apiSlice.ts`
- `layout`, `routes`, `styles`, `components` (כולל `components/ui`)
- `openapi/openapi.yaml`
- `SPEC.md` של המסך

### לא שייך לספרינט 1

- קבצי `*.test.tsx`. הבדיקות הן בספרינט 5.

### מה לבדוק בריוויו

- [ ] לכל מסך יש מצבי טעינה, ריק, שגיאה ואין הרשאה
- [ ] יש `usePermission('<מסך>')` ו-`canWrite(level)`, ופעולות שאין אליהן הרשאה מוסתרות
- [ ] כל הרכיבים מגיעים מ-`components/ui`, ואין ערכים שרירותיים כמו `bg-[#123456]` או `w-[372px]`
- [ ] אין רכיב חדש שנבנה בתוך המסך. רכיב חסר נכנס ב-PR נפרד
- [ ] במסך עם לשוניות, הלשונית הפעילה נשמרת ב-`?tab=`
- [ ] הטקסטים בעברית פשוטה ובלשון נקייה
- [ ] `npm run verify` ירוק, וה-PR פתוח ל-`dev`

---

## משימת הסכמות (ספרינט 2, חלק א'), בריפו `school-server`

ענף: `feature/<מפתח>-<מספר>-<מסך>-schema`, מתוך `dev`.

### קבצים שאמורים להשתנות

| קובץ | מתי |
|---|---|
| `src/modules/<אוסף>/model.ts` | רק כשבשורה "בבעלות המסך" ב-SPEC יש אוסף |
| `src/modules/<אוסף>/seed.ts` | אותו מקרה |
| `schema.ts`, `routes.ts` וקובץ בדיקה באותה תיקייה | נוצרים על ידי `npm run new:module`. בחלק א' הם צריכים להישאר כמו שהגנרטור יצר אותם |

אם האוסף כבר נמצא ב-`src/models`, צריך למחוק את `model.ts` מהמודול ולייבא את המודל מ-`../../models`.

### קבצים שאסור שישתנו

- `src/models`, `src/seed`, `permissions`, `middleware`, `openapi`
- `app.ts` או קובץ רישום של routes. הגנרטור מחבר את ה-router לבד, כך שאין סיבה לגעת בהם.

### צמד שאין לו אוסף "בבעלות המסך"

זה המצב בעשרה צמדים. אצלם אמור להיות PR ריק, או שאין PR בכלל. כל מה שהם צריכים לעשות הוא לבדוק את המודלים בליבה ולדווח ב-Slack מה חסר.

### מה לבדוק בריוויו

**במודל:**

- [ ] יש `institutionId` חובה, עם אינדקס
- [ ] השמות של השדות וערכי ה-`enum` זהים לחוזה
- [ ] תאריך בלי שעה נשמר כמחרוזת `'YYYY-MM-DD'`, עם `DATE_ONLY`
- [ ] לשדה שיכול להיות ריק יש `default: null`
- [ ] ה-`ref` מצביע על שם המודל (`'Student'`), ולא על שם האוסף
- [ ] יש אינדקס ייחודי על מה שאסור שיחזור
- [ ] למידע רגיש יש `select: false`
- [ ] יש `timestamps: true`

**ב-seed:**

- [ ] ה-seed אידמפוטנטי: `updateOne` עם `upsert: true` לפי מפתח טבעי, ולא `create` או `insertMany`
- [ ] הנתונים קבועים: אין `Math.random()`, ואין `new Date()` לתאריך של יום
- [ ] יש נתונים לכל מצב של המסך
- [ ] ה-seed עובד רק על מוסד הדוגמה (`name: 'בית ספר לדוגמה'`)
- [ ] אין מחיקה של נתונים מאוספים של צמדים אחרים

**בקוד:**

- [ ] הייבוא הוא רק מהליבה ומהתיקייה של הצמד. אין `../other-module/...`
- [ ] `npm run seed` רץ פעמיים, והמספרים לא משתנים
- [ ] `npm run verify` ירוק

---

## מה מצאתי בענפים הקיימים (2026-11-03)

| ענף | מה השתנה | הערה |
|---|---|---|
| `feature/PEDA-12-S01-client1` (קליינט) | רק `src/screens/S06-behavior-notes/index.tsx` (12 שורות, ב-commit שנקרא "commit") | שם הענף אומר S01, אבל הקובץ שהשתנה שייך ל-S06, ולפי לוח המשימות S06 שייך לצמד A-02. אין בענף קובץ API ואין mocks |
| `feature/PEDA-104-G04-schema` (שרת) | רק `src/modules/assessments/seed.ts` | לפי לוח המשימות, `assessments` הוא אוסף משותף שהצמד של G04 רק כותב אליו, ואין להם אוסף בבעלות המסך. מכאן שהם לא אמורים לבנות מודול `assessments`. את ה-seed שלו כבר יש בליבה, ונתונים חסרים צריך לבקש מראשת הצוות |

יש בשרת עוד ענפים שלא עברתי עליהם: `PEDB-104-T03-seed`, `b03-sprint1-schemas`, `b03-sprint2-institutions-get`, `b03-sprint2-terms-get`, `I01-institution-structure`, `grades-feature` ו-`seed-calendar-events`.
