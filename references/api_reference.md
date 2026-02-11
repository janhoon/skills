# Outline API Reference

Use this reference when creating, updating, searching, or organizing Outline content through API calls.

## Prerequisites

- Capture workspace base URL (for example `https://wiki.example.com`).
- Store API key in an environment variable such as `OUTLINE_API_KEY`.
- Send `Content-Type: application/json` and `Authorization: Bearer <token>` headers.
- Verify endpoint names and request fields against the deployed Outline version.

## Common Endpoints

These endpoints are commonly available in Outline API deployments:

- `POST /api/collections.list`
- `POST /api/documents.list`
- `POST /api/documents.search`
- `POST /api/documents.info`
- `POST /api/documents.create`
- `POST /api/documents.update`
- `POST /api/documents.delete`

## Standard Workflows

### Create Document

1. Resolve `collectionId` via `collections.list`.
2. Prepare markdown body with clear heading structure.
3. Call `documents.create` with `title`, `text`, and `collectionId`.
4. Persist returned `id` and URL for future updates.

Example payload:

```json
{
  "title": "Incident Runbook",
  "collectionId": "<collection-id>",
  "text": "# Incident Runbook\n\n## Triage\n...",
  "publish": true
}
```

### Update Document

1. Load existing page via `documents.info`.
2. Apply content edits while preserving required headings and links.
3. Send `documents.update` with `id` and updated fields.
4. Confirm the response revision or timestamp to verify save success.

Example payload:

```json
{
  "id": "<document-id>",
  "title": "Incident Runbook",
  "text": "# Incident Runbook\n\n## Triage\nUpdated steps...",
  "publish": true
}
```

### Search and Curate

1. Query with `documents.search` to discover duplicates and stale pages.
2. Create a merge or archive plan grouped by collection.
3. Execute updates before deletes to avoid broken references.

## Reliability Practices

- Avoid destructive operations before producing a migration or rollback note.
- Keep a local copy of previous markdown before bulk updates.
- Batch requests conservatively and inspect API errors between batches.
- Record who requested the change and why for auditability.
