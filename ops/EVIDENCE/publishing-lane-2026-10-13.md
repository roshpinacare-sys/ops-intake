# עדות-מסילה · Publishing lane — למה אפס פרסומים מהמסילות המשוערות

נמדד 2026-10-13T11:15–11:45Z · סריקה T23-C2 בתפקיד agent · כל הקביעות כאן נמדדו מריצות, לוגים ו-API בחלון זה. אין סטטוס-קבר — כל שורה נושאת קומיט, מזהה-ריצה או שורת-לוג מצוטטת.

## התמונה הנמדדת — שתי עובדות במקביל

1. **השרשרת חיה והפיד לא ריק.** condenser_api נמדד 200-OK משלושה צמתים (api.steemit.com / api.justyy.com / api.steem.fans) באותו ראש-שרשרת (~110,402,676–110,402,922 בחלון). חשבון headcorner: 1,123 פרסומים; חמישה פרסומים חדשים ביום זה — 07:21 / 08:17 / 09:12 / 10:09 / 11:19 UTC (get_discussions_by_blog, permlinks בשדה created מאומתים).
2. **המסילות המשוערות מפרסמות אפס.** שלוש המסילות שאמורות לשאת את הפרסום (steem/social-cadence, Domain/content-campaign, Domain/daily-digest) נמדדו אדומות או מציבות-מצבה. הפרסומים שעולים לשרשרת מגיעים דרך המו"ל הראשי (CR-0065 — Domain/human-cadence.cjs), לא דרך השערים למטה.

## שער (א) — steem פרטי: חומת-דקות Actions, אפס-צעדים

- ריצה 38325218691 (2026-10-13T00:02:08Z, workflow_dispatch): job `scan` — conclusion **failure**, **steps: 0**, 3 שניות מ-started ל-completed.
- כל ההרצות האחרונות של הריפו (total 1,124) — gitleaks, social-cadence, sovereign-cycle, ci-probe, token-forge — failure באותה חתימה (00:02:01–00:02:07).
- חתימה זו היא החוק המקודד QUOTA-SIGNATURE (probe.mjs T-73): אפס צעדים שהושלמו תוך <15 שניות = שער-חיוב, לא כשל-קוד. המדידה כאן מאשרת את החוק על ריפו זה.
- המשמעות: כל וורקפלו בריפו הפרטי מת, לפני שורת-קוד ראשונה. איברים ציבוריים רצים ללא הגבלה — זה שער-בעלים, לא תיקון-סוכן.

## שער (ב) — social-cadence: מצבה מוצהרת (ציטוט מה-header שנקרא)

מקור: `steem/.github/workflows/social-cadence.yml` — "SOCIAL-CADENCE — מצבה-כנה (tombstone) · zai-13d · trace 1a12d186d2f8b86d", מדידה מקורית 2026-10-11 (WO-unwired-steem-social-cadence P2):

- מצביע-מת כפול: ה-workflow מניח cadence-run.cjs תחת Domain/agents/social — שם 3 קבצים בלבד ואין cadence-run.cjs; העותק התקין ב-steem/agent/social/; אפס-ריצות מעולם (census + API).
- שער-שידור: המפעיל מנוע-שידור במהותו (steem.broadcast.send עם SA_FLEET_KEYS) — חוקי-הצי אוסרים שידור מ-lanes (מנדט 13-d) והמו"ל-היחיד הוא CR-0065; SA_FLEET_KEYS אינו בריפו. הערה-כנות מהסריקה של היום: קריאת /secrets מוותכן-סוכן זה מחזירה רשימה ריקה (היקף-הרשאה לא כולל קריאת-סודות), ולכן הראיה היא המדידה המצוטטת ב-header (MAIN_KEY, ZIP_PAT — בלי SA_FLEET_KEYS) ולא רשימה עכשיו.
- ראיה נוספת מה-header: ריצת DRY מקומית (2026-10-11, node 24 + steem@0.7.11) קורסת ב-measure() — קריאת rc_api ב-4-ארגומנטים מול ספרייה שמצפה 3 → `TypeError: callback is not a function`. selftest המסילה עצמה: 38 PASS · 0 FAIL.
- המצבה עצמה fail-open ומדווחת — exit 0, אפס מפתחות, אפס שידור. היא נכונה לכתב-היא.

## שער (ג) — Domain: content-campaign ו-daily-digest, שרשרת vault-selfauth

content-campaign — ריצה 38353510156 (2026-10-13T05:50:55Z, failure), מהלוג (שורות מצוטטות, ללא ערכי-סודות):

- השלב "Vault self-auth (T-47 chain)" עצמו ירוק; הכשל ב-"Daily themed post (posting-only, measured, idempotent)".
- סולם-הכנות בסקריפט, כפי שנכתב ורץ: `[vault-selfauth] no PAT secret — honest skip` → `clone failed — honest skip to repo secrets` → `unseal failed — honest skip to repo secrets` → `[vault-selfauth] no session pass — honest skip` → `[vault-selfauth] WIF slot absent — honest skip` — כל ערוץ פותח ערוץ-גיבוי, ואילו שנכשל מדווח בשמו ומדלג.
- פסק-הדין המדוד של שער-הביטים: `VAULT_STEEM_POSTING_WIF (self-auth chain): לא סופק`; `STEEM_POSTING_WIF: אינו רשות-ה-posting של @owner — נדחה`; `WEAVE_STEEM_WIF: אינו רשות-ה-posting של @owner — נדחה`; ומסקנה: `אף סוד שסופק אינו רשות-ה-posting — סירוב אדום: לא משדרים בשם מי שלא אישרו.` כמו-כן `CONTENT_DRY: 1` (מצב-יבש).
- קריסה נלווית נמדדה בלוג: `at Function.fromWif (steem/lib/auth/ecc/src/key_private.js:196:31)` מ-pickWif (agents/content-campaign.mjs:439) — ערך שאינו מפתח-WIF תקין מפיל תהליך במקום דילוג-כנה.

daily-digest — ריצה 38362501786 (2026-10-13T07:25:01Z, failure, dry=false): אותו פסק-דין — `STEEM_POSTING_WIF: אינו רשות-ה-posting של @*** — נדחה`, `WEAVE_STEEM_WIF: … נדחה`, `אף סוד שסופק אינו רשות-ה-posting — סירוב אדום`; קריסה ב-pickWif (agents/daily-digest.mjs:192).

מסקנת-השער: השרשרת העצמית של אחזור-WIF מכספת מדווחת כנה בכל ערוץ; הערוץ שמסתיים בריק נמדד כריק; והסודות שכן מוזרמו בריפו אינם רשות-הפרסום של החשבון — השער מסרב נכון ואינו משדר בשם לא-מאושר.

## שער (ד) — מה פותח את המסילה (החלטות-בעלים, במדויק)

1. **חומת-הדקות ב-steem הפרטי** — החלטת-חיוב בעלים בלבד: הגדלת מיטב-ההוצאה/מעבר-חיוב, או העברת המסילות לעמוד-חישוב ציבורי (הצי מוכיח מדידתית שאיברים ציבוריים רצים ללא הגבלה).
2. **שחרור CR-0065 למו"ל-גיבוי** — מילת-בעלים; בלעדיה social-cadence נשאר מצבה לפי חוקי-הצי עצמם.
3. **הזרמת SA_FLEET_KEYS לריפו steem** — פעולת-סודות של הבעלים (מנוהלת ב-API, לא בקבצים).
4. **תיקון קריאת rc_api ל-3-ארגומנטים** ב-agent/social/cadence-run.cjs — צעד-קוד שה-header עצמו מגדיר "צעד-בעלים יחד עם החימוש".
5. **תיקון הנתיב ב-workflow לעותק-steem עצמו** (בלי clone-Domain) — סעיף (ד) ב-header.
6. **WIF רשות-פרסום תקין** ל-content-campaign/daily-digest — הסודות הקיימים נדחו מדידתית כלא-רשות; נדרש סוד נכון או כספת שמנפיקה אותו (בלי זה הסירוב-האדום יישאר — והוא התנהגות-נכונה).
7. **fail-soft ב-pickWif** (content-campaign.mjs:439, daily-digest.mjs:192) — קריסה על ערך לא-תקין במקום דילוג-כנה; לתיקון בליין-Domain, לא במסילה זו.

## הערות-כנות

- הריצות שנבדקו הן האחרונות של כל workflow; חלון-הלוג שהורד הוא הריצה הכושלת האחרונה של content-campaign ושל daily-digest.
- לא נמדד כאן מי בדיוק חותם את פרסומי headcorner — נמדד שהם עולים בקצב כשעה ושאף אחד משלושת השערים לא יכול היה להוציא אותם היום.
- שמות-חשבונות בשדות המדידה הוצגו כפי שהלוג הציג אותם (עצמו מצונן); לא צוטט ערך-סוד, מסוכם או מפתח — רק שמות-משתנים.
- בריאות-השרשרת עצמה (צמתים, ראש, קופה) נמדדה באותו חלון ומדווחת בהמשך ל-Pulse — ראו ops/EVIDENCE/network-scouts-2026-10-13.md והטבלה בעבודת T23-C2.
