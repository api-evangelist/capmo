---
name: File a defect ticket with a photo attachment
description: Create a Capmo project ticket (defect/task) with the right category and type, then attach a photo via the two-step upload flow.
api: openapi/capmo-rest-api-openapi-original.json
operations: [getProjectTicketCategories, getProjectTicketTypes, createProjectTicket, getTicketAttachmentUploadUrl, createProjectTicketAttachment, getProjectTicket]
generated: '2026-07-18'
method: generated
---

# File a defect ticket with a photo attachment

Capture a site defect as a Capmo ticket and attach a photo.

## Auth
`Authorization: Capmo <API_KEY>` on every call.

## Steps
1. List categories with `getProjectTicketCategories`
   (GET `/api/v1/projects/{projectId}/ticket-categories`) and types with
   `getProjectTicketTypes` (GET `/api/v1/projects/{projectId}/ticket-types`); pick ids.
2. Create the ticket with `createProjectTicket`
   (POST `/api/v1/projects/{projectId}/tickets`), referencing the chosen category/type.
   Keep the returned ticket `id`.
3. Get an upload URL with `getTicketAttachmentUploadUrl`
   (POST `/api/v1/upload/ticket-attachments`).
4. `PUT` the photo bytes to that upload URL (direct storage upload — not the Capmo API host).
5. Register the attachment with `createProjectTicketAttachment`
   (POST `/api/v1/projects/{projectId}/tickets/{ticketId}/attachments`) referencing the
   uploaded file.
6. Verify with `getProjectTicket` (GET `/api/v1/projects/{projectId}/tickets/{ticketId}`).

## Conventions
- Uploads are always two-step: request an upload URL, PUT the bytes, then create the
  record (same pattern for documents and plans — see `conventions/capmo-conventions.yml`).
- Ticket comments use a reduced token-only pagination (`after` + `limit`, no `total`).
- Errors: `{type, message, errors:[{code, message}]}` (`errors/capmo-problem-types.yml`).
