# הבריאות שלי (My Health)

אפליקציית מעקב בריאות אישית בעברית (RTL) — ירידה במשקל, בניית שריר, תזונה, אימונים ושינה. קובץ HTML יחיד, ללא שרת וללא build.

| | |
|---|---|
| **אתר חי** | https://mbbtower4-boop.github.io/my-health/ |
| **מאגר** | https://github.com/mbbtower4-boop/my-health |
| **מיקום מקומי** | `D:\Work\AI\my-health` |
| **קובץ יחיד** | `index.html` — כל ה-HTML/CSS/JS בפנים |
| **תוכנית ומעקב** | `PLAN.md` — נתוני בסיס, חישוב קלוריות, תפריט, תוכנית אימונים, יומן פרויקט |
| **הרצה** | פתיחת `index.html` בדפדפן או באתר החי; כל push ל-master מתפרסם ב-GitHub Pages תוך דקה-שתיים |

## ארכיטקטורה

- **אין backend.** כל הנתונים ב-`localStorage` תחת המפתח `my-health-v1`, כאובייקט `db` אחד. הנתונים פר-מכשיר; העברה בין מכשירים דרך "גיבוי ושחזור" (קובץ JSON או העתקת טקסט).
- SPA וניל: חמישה מסכים (`#v-today`, `#v-food`, `#v-train`, `#v-stats`, `#v-settings`), כל אחד נבנה מחדש ע"י פונקציית `render*()` שמציבה `innerHTML`. טפסים ב-sheets תחתונים. אירועים ב-`onclick` inline לפונקציות גלובליות.
- מאגר המאכלים ההתחלתי (`SEED_FOODS`) וארסנל התרגילים (`SEED_EX`) מקודדים בקובץ עם מזהים יציבים (`s0…`, `e0…`); מאכלים/תרגילים אישיים נשמרים ב-`db.foods` / `db.exercises` עם מזהי `c_*`. אין למחוק או לסדר מחדש פריטים ב-seed — היומנים מפנים אליהם לפי מזהה.
- גרפים: SVG inline (`lineChart`) ופסים ב-div (`barChart`), תמיד LTR כרונולוגי.

## מבנה הנתונים (`db`)

| שדה | תוכן |
|---|---|
| `profile` | `{sex, age, height, startWeight, startDate, goalWeight, goalDate, activity}` |
| `targets` | `{kcal, protein, fat, carbs, steps, sleep, water, fastHours, workouts}` |
| `days` | `'YYYY-MM-DD' → {weight, waist, sleepH, sleepQ, steps, water, fastH, note}` |
| `foods` | מאכלים אישיים `{id, name, cat, per (100 או 1), kcal, p, f, c}` |
| `foodLog` | `{id, date, meal (m1/m2/m3/snack), foodId, name, per, grams, kcal, p, f, c}` — הערכים כבר מחושבים לכמות |
| `exercises` | תרגילים אישיים `{id, name, muscle}` |
| `templates` | `{id, name, exercises:[exId]}` |
| `active` | אימון פעיל `{id, date, name, tplId, startedAt, exercises:[{exId, sets:[{w, r, done, pw, pr}]}]}` (`pw/pr` = ערכי הפעם הקודמת להצגה כ-placeholder) |
| `workouts` | `{id, date, name, tplId, exercises:[{exId, sets:[{w, r}]}], note, durationMin, prs}` — רק סטים שסומנו ✓ |
| `cardio` | `{id, date, type, min, km, note}` |
| `fast` | `{start: timestamp \| null}` |
| `recentFoods` | מזהי מאכלים אחרונים (עד 12) |

## כללי עבודה

- כל שינוי גלוי למשתמש: לעדכן `APP_VERSION` ב-`index.html` **וגם** `version` ב-`package.json` (חייבים להיות זהים), להוסיף רשומה ב-`CHANGELOG.md`, ולציין את הגרסה בהודעת הקומיט.
- Light mode בלבד (`color-scheme: light only`) — לשמור.
- החישובים: Mifflin-St Jeor ל-BMR; TDEE = BMR × מקדם פעילות; יעד קלורי = TDEE − גירעון הנדרש לקצב הירידה (מינימום 300); 1RM משוער לפי Epley. זה כלי מעקב, לא ייעוץ רפואי.
