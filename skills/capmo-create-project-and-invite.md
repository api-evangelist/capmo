---
name: Create a project and invite a member
description: Create a Capmo construction project (optionally from a template), then add a contact and invite them to the project with a permission set.
api: openapi/capmo-rest-api-openapi-original.json
operations: [validate, getProjectTemplates, createProject, getProjectPermissionSets, createProjectPersonFromOrganisation, inviteProjectPerson]
generated: '2026-07-18'
method: generated
---

# Create a project and invite a member

Use the Capmo REST API (`https://api.capmo.de`, base path `/api/v1`) to stand up a
new project and bring a teammate onto it.

## Auth
Send every request with `Authorization: Capmo <API_KEY>`. Confirm the key first
with `validate` (GET `/api/v1/validate`) — it returns the associated user ID.

## Steps
1. (Optional) List templates with `getProjectTemplates` (GET `/api/v1/project-templates`)
   and note a `project_template_id`.
2. Create the project with `createProject` (POST `/api/v1/projects`). Pass
   `project_template_id` to clone a template (this can take a few seconds) or omit it
   for a blank project. The API user is added as a full-access member automatically.
   Keep the returned project `id`.
3. Add the person to the project contact book with
   `createProjectPersonFromOrganisation` (POST `/api/v1/projects/{projectId}/people-from-organisation`)
   using an existing organisation person, and keep the returned person `id`.
4. Fetch permission sets with `getProjectPermissionSets`
   (GET `/api/v1/projects/{projectId}/permission-sets`) and pick a `permission_set_id`.
5. Invite them with `inviteProjectPerson` (POST `/api/v1/projects/{projectId}/memberships`),
   passing the person id and the chosen `permission_set_id`.

## Conventions
- List calls are cursor-paginated: use `after` / `limit` and read `data.after`,
  `data.count`, `data.total` (see `conventions/capmo-conventions.yml`).
- No idempotency-key is supported; do not blindly retry `createProject` on timeout —
  re-list projects to check whether it succeeded.
- Errors come back as `{type, message, errors:[{code, message}]}`
  (see `errors/capmo-problem-types.yml`). A `403` means the API-key user lacks access.
- Rate limit is 600 requests/minute; honour `x-rate-limit-reset` on `429`.
