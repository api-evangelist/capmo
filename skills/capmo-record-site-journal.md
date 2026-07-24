---
name: Record a daily site journal
description: Create and retrieve a Capmo construction site journal (Bautagebuch) entry with attendances, visits and weather observations for a project.
api: openapi/capmo-rest-api-openapi-original.json
operations: [validate, getProjects, createSiteJournal, getSiteJournalsPaginated, getSiteJournal]
generated: '2026-07-18'
method: generated
---

# Record a daily site journal

Log a day on site as a Capmo site journal (Bautagebuch).

## Auth
`Authorization: Capmo <API_KEY>` on every call; `validate` (GET `/api/v1/validate`)
to confirm the key.

## Steps
1. Find the project with `getProjects` (GET `/api/v1/projects`) — cursor-paginated with
   `after` / `limit`. Keep the project `id`.
2. Create the journal with `createSiteJournal`
   (POST `/api/v1/projects/{projectId}/site-journals`), supplying the date plus optional
   `attendances`, `visits` and `weather` observations.
3. List entries with `getSiteJournalsPaginated`
   (GET `/api/v1/projects/{projectId}/site-journals`) or read one with `getSiteJournal`
   (GET `/api/v1/projects/{projectId}/site-journals/{siteJournalId}`).

## Conventions
- On `updateSiteJournal`, object properties (attendances/visits/weather) are **replaced
  wholesale**, not merged — send the full desired set each time.
- No idempotency-key support; re-list before retrying a create after a timeout.
- Errors: `{type, message, errors:[{code, message}]}` (`errors/capmo-problem-types.yml`).
- Rate limit 600/min; honour `x-rate-limit-reset` on `429`.
