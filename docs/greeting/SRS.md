# SRS — Greeting

Module: `greeting`
Last updated: 2026-05-27
Design: [View the approved design](http://localhost:8080/design/368ab16d-a7b8-4230-9647-28411080e6e9)
Design system: `design/design-system.md`

> One file per module, at `docs/{module}/SRS.md`. It covers only the functions
> that belong to this module. Never write `docs/SRS.md`.

## 1. Purpose

The greeting module displays a single line of text — "Hello Word" — on the
site's one page. The text is stored in PostgreSQL and served to the page through
a read-only REST endpoint. Without this module there is no site: it is the
entire product, and it proves the full-stack path (database → API → page) works.

## 2. Actors

| Actor | Who they are | What they may do in this module |
|---|---|---|
| Guest | Any visitor, not signed in | Open the page and read the greeting text |

There are no other actors. No accounts, no admin, no editing.

## 3. Scope

**In scope** — the functions specified below, by their plan titles:

- Display greeting from database

**Out of scope** — name what a reader would reasonably expect here and say
where it lives instead.

- Creating or editing the greeting from the UI — deliberately not built; the
  value is seeded in the database and read-only.
- Authentication and accounts — deliberately not built; the page is public.
- Any second page, navigation, forms, or admin panel — deliberately not built.
- Search, pagination, or listing multiple greetings — deliberately not built;
  there is exactly one greeting row.

## 4. Functional requirements

### 4.1 Display greeting from database

**Requirement GREETING-001 — Serve the greeting text**

*As a* Guest, *I want to* open the page and see the greeting text, *so that* I
can read the one message the site exists to show.

Behaviour:

1. The database is seeded with exactly one greeting row whose text value is
   "Hello Word".
2. The backend exposes the greeting as a read-only resource returning that
   text.
3. The page requests the greeting from the backend and renders the returned
   text as the page's single visible line.
4. Reloading the page re-reads the current value from the database and shows it.

**Acceptance criteria** — each maps one-to-one onto a test case in
`docs/greeting/test-cases/display-greeting-from-database.md`.

| # | Given | When | Then |
|---|---|---|---|
| AC-1 | The database holds one greeting row with text "Hello Word" | A Guest opens the page | The page shows the text "Hello Word" |
| AC-2 | The page is already open and has rendered the greeting | The Guest reloads the page | The page re-reads the greeting and shows the same "Hello Word" text |

**Failure, boundary and permission behaviour**

| Case | Condition | Expected behaviour |
|---|---|---|
| Empty | The greeting row is missing or its text is empty | The page shows the empty state ("no greeting yet"), not a blank screen |
| Upstream failure | The database is unreachable or the API request fails | The page shows the error state with a retry action; no stale value is silently shown as if current |
| Slow load | The request has not yet completed | The page shows the loading state, then the loaded text when it arrives |
| Permission | Not applicable — the resource is public read-only; no actor is denied | No permission check exists |

**Data touched** — the fields this function reads and writes, in product terms.

| Field | Type | Required | Rule |
|---|---|---|---|
| greeting text | text | yes | Exactly the value "Hello Word" when seeded; non-empty, max 255 characters |

## 5. Screens

| Screen | Section in the design | Functions it serves | States that must exist |
|---|---|---|---|
| Greeting | Single full-page screen | GREETING-001 | default (loaded), loading, empty, error |

## 6. Non-functional requirements

| Area | Requirement |
|---|---|
| Performance | The greeting renders within 2s of page load on a typical connection |
| Accessibility | The greeting text is plain text with contrast ≥ 4.5:1 (near-black on white) |
| Responsive | The page works at 320px and up; no horizontal scroll |
| Localisation | Copy is exactly "Hello Word" as the stakeholder wrote it |
| Privacy | No personal data is stored or collected |

## 7. Dependencies and assumptions

- **Depends on:** PostgreSQL, for storing the seeded greeting value.
- **Assumption:** the greeting text is seeded once and treated as read-only.
  If it must become editable later, that is new scope and a new plan item.

| Open question | Proposed default | Who decides |
|---|---|---|
| None — no behaviour is left undecided | — | — |

## 8. Traceability

| Plan item | Requirement ids | Test cases |
|---|---|---|
| Display greeting from database | GREETING-001 | `test-cases/display-greeting-from-database.md` |
