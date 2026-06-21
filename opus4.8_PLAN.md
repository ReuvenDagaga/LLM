# opus4.8 — Master Plan

**מטרה:** 3000 דוגמאות full-stack ברמה הגבוהה ביותר שאפשר לייצר, שמלמדות מודל קטן להתנהג כמו מודל ענק ("40T") בפול-סטאק. 30 סוכנים, כל אחד כותב 100 דוגמאות מהשורש, עם השקעה מקסימלית ובדיקת איכות.

**הבחירה שנבחרה (המושלם ביותר):** פורמט **`fs` עשיר** (כמו `fs-001`) — קוד `before`/`after` אמיתי, `reasoning` עם חלופות שנדחו ולמה, `migration_strategy`, `testing_strategy`, `estimated_complexity`. 3000 דוגמאות = 30 batches × 100. עומק מכויל כך שכל סוכן באמת מסוגל להוציא 100 דוגמאות חזקות בלי לקרוס לפילר.

> סטטוס: **תכנית בלבד.** שום סוכן לא הורם, שום קובץ דאטה לא נכתב. ממתין לאישור "רוץ".

---

## 1. למה הפורמט הזה (ולא poly/rule)

ב-dataset הקיים יש שני פורמטים:

| פורמט | דוגמה | איכות אמיתית |
|---|---|---|
| `poly`/`rule`/`scenario` (טמפלייטי) | `poly-201`, `fullstack-logic-rule-0001`, `large-code-systems-0001` | פרוזה חוזרת על עצמה בין `reasoning`/`why`/`decision_framework`/`validation_plan`. "filler" למרות `quality_score:96-100`. |
| **`fs` עשיר** | `fs-001` | קוד before/after אמיתי, issues קונקרטיים, tradeoffs שנדחו עם נימוק, migration + testing + complexity. **זה מה שמלמד יכולת אמיתית.** |

הסכמה ב-`dataset_all_gold` הטרוגנית (היא מאחדת פורמטים שונים), אז דוגמאות בפורמט `fs` נכנסות drop-in בלי לשבור כלום.

---

## 2. הסכמה המדויקת (תואם `fs-001` 1:1)

כל שורה ב-JSONL = אובייקט אחד עם השדות הבאים (סדר חופשי, אבל כולם חובה):

```jsonc
{
  "id": "opus48-<shard:02d>-<n:03d>",          // ייחודי גלובלית, למשל "opus48-07-042"
  "scenario_type": "<archetype>",               // ראה §4
  "difficulty": "advanced" | "expert" | "staff",// הטיה ל-expert/staff
  "title": "<משפט פעולה ספציפי>",               // ייחודי בתוך ה-shard
  "business_context": "<2-5 משפטים: למה זה קורה, מי המשתמשים, מה הכאב, מספרים אמיתיים>",
  "constraints": [ "<4-6 אילוצים קשים ומציאותיים>" ],
  "before_state": {
    "description": "<מצב נוכחי + למה הוא בעייתי>",
    "files": [
      { "path": "src/...", "content": "<קוד אמיתי מוגבל/באגי>", "issues": [ "<בעיה קונקרטית>", "..." ] }
      // 2-4 קבצים
    ]
  },
  "reasoning": "<הגישה הנאיבית + לפחות חלופה אחת שנדחתה ולמה + המסלול הנבחר כ-trace חוצה-שכבות (UI→API→data→async→config→tests→rollout)>",
  "after_state": {
    "description": "<מצב היעד>",
    "files": [
      { "path": "src/...", "content": "<קוד מתוקן/חדש אמיתי>", "purpose": "<מה הקובץ פותר>" }
      // 3-6 קבצים
    ]
  },
  "migration_strategy": [ "<4-6 צעדים מדורגים והפיכים: flag → canary → ramp → cleanup>" ],
  "testing_strategy": "<פיקסצ'רים בצורת פרודקשן, אסרטים על side-effects (db/cache/events), בדיקת negative/chaos אחת לפחות>",
  "what_breaks_naive_approach": "<מה בדיוק נשבר בגישה הנאיבית בגבול הספציפי הזה>",
  "estimated_complexity": { "files_touched": <int>, "loc_changed": <int>, "person_days": <number> }
}
```

---

## 3. סף האיכות (bar שכל דוגמה חייבת לעבור)

1. **קוד אמיתי, לא פסאודו** — `before`/`after` עם קוד syntactically-plausible ונכון. אין `// ...TODO` כתחליף ללוגיקה.
2. `before_state.files`: 2-4 קבצים עם קוד מוגבל/באגי + `issues` קונקרטיים (לא "no tests" גנרי).
3. `after_state.files`: 3-6 קבצים עם קוד מתוקן + `purpose` ספציפי לכל קובץ.
4. `reasoning` **חייב**: (א) לנקוב בגישה הנאיבית, (ב) לדחות לפחות חלופה אחת **עם הסיבה**, (ג) trace חוצה-שכבות.
5. `constraints`: 4-6 אילוצים קשים (concurrency, backward-compat, scale numbers, auth, multi-instance...).
6. `migration_strategy`: 4-6 צעדים מדורגים והפיכים (אחורה תמיד אפשרי).
7. `testing_strategy`: פיקסצ'ר בצורת פרודקשן, אסרט על פלט **וגם** על side-effects, ובדיקת כשל/chaos אחת.
8. `difficulty` ∈ {advanced, expert, staff} — הטיה חזקה ל-expert/staff.
9. **אפס שכפול**: אין שתי דוגמאות באותו shard עם `title` או core-scenario זהה.
10. **אפס פילר**: כל משפט ספציפי לתרחיש — לא ניסוח-תבנית שאפשר להעתיק בין דוגמאות.

---

## 4. 30 ה-Shards (כל סוכן = shard אחד = 100 דוגמאות)

כל shard הוא תחום full-stack מובחן. בתוך ה-shard הסוכן **מגוון** stack, domain-entity, difficulty ותרחיש-משנה כדי ש-100 הדוגמאות לא יהיו חזרה. כל shard חוצה שכבות (UI+API+data+infra+tests).

| # | shard / נושא | `scenario_type` עיקרי | דגשי stack/domain |
|---|---|---|---|
| 01 | State architecture overhaul (React) | `state_overhaul` | context splitting, useSyncExternalStore, reducers, derived/optimistic state |
| 02 | Frontend rendering performance | `performance_crisis` | memoization, virtualization, code-split, RSC/hydration, Next.js |
| 03 | Forms & validation at scale | `feature_addition_with_refactor` | schema-driven forms, async/field-level validation, optimistic UI |
| 04 | Accessibility & i18n overhaul | `accessibility_i18n` | a11y semantics, focus mgmt, RTL, intl/locale formatting |
| 05 | Realtime collaborative UI (CRDT/OT) | `realtime_e2e` | presence, cursors, conflict resolution, Yjs/Automerge |
| 06 | 3D / data-viz integration | `3d_integration` | Three.js/WebGL, instancing, LOD, canvas perf, WebGPU |
| 07 | Offline-first / PWA / sync | `offline_sync` | service workers, IndexedDB, background sync, reconciliation |
| 08 | API design & versioning | `api_versioning` | contract-first, backward-compat, deprecation, OpenAPI, pagination |
| 09 | DB modeling & zero-downtime migrations | `architecture_migration` | schema evolution, online migrations, indexing, partitioning |
| 10 | Transactions & consistency | `data_consistency` | sagas, idempotency, outbox, exactly-once, distributed tx |
| 11 | Caching strategy | `performance_crisis` | multi-layer cache, invalidation, stampede protection, Redis/CDN |
| 12 | Background jobs & queues | `background_jobs` | workers, retries, DLQ, backpressure, BullMQ/Kafka |
| 13 | Event sourcing / CQRS | `event_sourcing` | aggregates, projections, replay, eventual consistency |
| 14 | Search & relevance | `feature_addition_with_refactor` | full-text/vector, ranking, faceting, ES/pgvector |
| 15 | GraphQL federation & N+1 | `performance_crisis` | dataloaders, schema stitching, persisted queries, complexity limits |
| 16 | DDD / anemic→rich model migration | `architecture_migration` | aggregates, bounded contexts, invariants in domain layer |
| 17 | Monolith decomposition (strangler) | `architecture_migration` | service extraction, module boundaries, anti-corruption layer |
| 18 | WebSocket / realtime scaling | `realtime_e2e` | horizontal scale, Redis adapter, sticky sessions, presence@scale |
| 19 | Performance crisis / profiling | `performance_crisis` | N+1, memory leaks, hot paths, flamegraphs, load shedding |
| 20 | Observability | `feature_addition_with_refactor` | tracing, structured logs, metrics, SLOs, OpenTelemetry, correlation IDs |
| 21 | Rate limiting & quotas | `security_hardening` | sliding window, token bucket, per-tenant/IP/device, abuse |
| 22 | DevOps / IaC / progressive delivery | `devops_delivery` | blue-green, canary, rollback, CI/CD, k8s, feature flags |
| 23 | Infra resilience | `feature_addition_with_refactor` | circuit breakers, retries, timeouts, bulkheads, graceful degradation |
| 24 | AuthN/AuthZ overhaul | `security_hardening` | JWT/sessions, SSO/OAuth/OIDC, RBAC/ABAC, token rotation |
| 25 | Security hardening (OWASP) | `security_hardening` | mass-assignment, CSRF/CORS, injection, SSRF, secrets |
| 26 | Multi-tenancy | `multi_tenancy` | tenant isolation, RLS, per-tenant config/theming, noisy-neighbor |
| 27 | Payments & billing | `feature_addition_with_refactor` | idempotent payments, webhooks, reconciliation, subscriptions |
| 28 | File pipeline / media | `feature_addition_with_refactor` | uploads, presigned URLs, virus scan, image processing, streaming |
| 29 | Testing strategy overhaul | `testing_overhaul` | production-shaped fixtures, contract/e2e/chaos, flaky elimination |
| 30 | Feature flags & experimentation | `feature_flags` | flag lifecycle, A/B, gradual rollout, kill switches |

**גיוון stacks לאורך הכל:** Node/Express, NestJS, Next.js/React, Vue/Nuxt, Python/FastAPI/Django, Go, Postgres, MongoDB, Redis, Kafka, gRPC.
**גיוון domains:** e-commerce, fintech, healthcare, social, devtools, gaming, IoT, B2B SaaS, logistics.

---

## 5. ארכיטקטורת ה-Workflow (generate → audit, pipeline)

```
Phase 1 — Generate (30 agents, general-purpose)
  shard i → agent כותב opus4.8_batch_{i:02d}.jsonl עם 100 רשומות.
            הסוכן עובד ב-4 מעברים פנימיים של 25 (append), ומאמת
            שכל שורה היא JSON תקין לפני הכתיבה.
            מחזיר: { shard, file, count, scenario_types[], stacks[] }

Phase 2 — Audit & Repair (30 agents, pipelined — מתחיל ברגע ש-gen i סיים)
  shard i → agent קורא את הקובץ, מאמת JSON+schema, מסיר פילר/כפילויות,
            מתקן באגים ברורים בקוד, מוודא בדיוק 100 רשומות תקינות,
            כותב מחדש. מחזיר: { file, final_count, fixed, quality_score }
```

- **למה pipeline ולא barrier:** audit של shard i לא תלוי ב-shards אחרים → מתחיל מיד כשה-gen שלו נגמר. wall-clock = השרשרת האיטית ביותר, לא סכום.
- **קונקרנטיות:** ה-workflow מריץ ~10-14 סוכנים במקביל; 60 הסוכנים (30+30) עוברים בתור. תקרת total agents (1000) רחוקה.
- **כל סוכן כותב קובץ נפרד** (`opus4.8_batch_01..30`) → אין התנגשות כתיבה, אין צורך ב-worktree isolation.
- **agentType:** `general-purpose` (כלי `*`) כדי שיוכלו Write + להריץ python לאימות.

### Post-workflow (main loop, אני):
1. אימות `wc -l` של כל 30 הקבצים = **3000** בדיוק.
2. כל שורה `json.loads` בלי שגיאה; כל המפתחות החובה קיימים.
3. סריקת `title` כפול חוצת-shards (להסרת חפיפה).
4. כתיבת `opus4.8_report.json`: התפלגות `scenario_type` / `difficulty` / stack, ספירות, תוצאות אימות.
5. **לא נוגעים** ב-`dataset_all_gold_*` אלא אם תבקש מיזוג מפורש.

---

## 6. תוצרים (Deliverables)

- `opus4.8_batch_01.jsonl` … `opus4.8_batch_30.jsonl` — 100 כל אחד = **3000**.
- `opus4.8_report.json` — דוח איכות/התפלגות/אימות.
- `opus4.8_PLAN.md` — המסמך הזה.

---

## 7. סיכונים ומיטיגציות

| סיכון | מיטיגציה |
|---|---|
| 100 דוגמאות עמוקות לסוכן → ירידת איכות לקראת הסוף | 4 מעברי-25 פנימיים + שלב audit/repair נפרד |
| JSON לא תקין מכתיבה ידנית | אימות כל שורה לפני append; audit מאמת שוב; כתיבה דרך python `json.dumps` |
| חפיפה בין shards | נושאים מובחנים + סריקת `title` כפול ב-post |
| עלות tokens גבוהה | מקובל — ביקשת השקעה מקסימלית (effort=max) |
| כתיבות מקבילות | כל סוכן בעל קובץ ייחודי → אין conflict |

**הערכת היקף:** ~3000 × ~3-5KB ≈ 9-15MB output. גדול אבל תקין.

---

## 8. נקודת ההחלטה הבאה

האם להריץ עכשיו? אם כן — אני מרים את ה-Workflow (Phase 1+2), ואחריו מריץ את האימות וכותב את הדוח. תכתוב **"רוץ"** ואני יוצא לדרך.
