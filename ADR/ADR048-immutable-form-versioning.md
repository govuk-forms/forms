# ADR048: Immutable Form Versioning

Date: 2026-03-24

## Status

Accepted

## Context

GOV.UK Forms currently uses a mutable document-based system for managing form lifecycle states. The `Form` model has an associated `FormDocument` model, with documents tagged as `draft`, `live`, or `archived`. The current API (v2) exposes these via:

- `GET /api/v2/forms/:form_id/draft`
- `GET /api/v2/forms/:form_id/live`
- `GET /api/v2/forms/:form_id/archived`

The `FormStateMachine` manages transitions between states (`draft`, `live`, `live_with_draft`, `archived`, `archived_with_draft`), and the `FormDocumentSyncService` synchronises the JSON content into `FormDocument` records when these transitions occur.

This approach has several limitations:

1. The `/live` endpoint is mutable. The content behind `/api/v2/forms/:form_id/live` changes each time a form is re-published, so consumers must always re-fetch. This prevents effective caching.
2. No explicit link between a submission and the form version it was submitted against. When a form is updated and re-published, there is no reliable way to identify which version of the form a given submission relates to. This makes it difficult to group submissions by form version or detect when a form changed between batch submission deliveries.
3. Mid-journey disruption. If a form creator publishes a new version or archives a form while a user is part-way through filling it in, the form can change or disappear mid-journey. There is no mechanism for in-progress users to continue with the version they started.

## Decision

We will introduce an immutable versioning model for published forms, exposed through a new v3 API. The key changes are:

### New API endpoints


| Endpoint                                            | Description                                                                                              |
| --------------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| `GET /api/v3/forms/:form_id/draft`                  | Returns the current draft form document JSON (mutable, changes as the form creator edits)                |
| `GET /api/v3/forms/:form_id/versions/:form_version` | Returns an immutable, versioned form document. Once created, this content never changes.                 |
| `GET /api/v3/forms/:form_id/latest`                 | Returns the latest live version of the form (a redirect or alias to the most recently published version) |


### Lifecycle

- Draft state: A form being edited has a draft available at `/api/v3/forms/:form_id/draft`. This behaves similarly to today.
- Publishing (making live): When a form is made live, a new immutable version is created and assigned an incrementing version identifier (e.g. `1`, `2`, `3`). It becomes available at `/api/v3/forms/:form_id/versions/:form_version`. The `/api/v3/forms/:form_id/latest` endpoint points to this new version.
- Archiving: When a form is archived, `/api/v3/forms/:form_id/latest` and `/api/v3/forms/:form_id/draft` return `404` (or `410 Gone`). However, all previously published versions remain available at `/api/v3/forms/:form_id/versions/:form_version` because they are immutable.

### Content removal

Immutability prevents deletion as part of normal operations. A separate process will be needed for exceptional cases where published content genuinely must be removed (e.g. GDPR erasure).

## Consequences

### Positive

- Cacheable published forms. Versioned form documents at `/api/v3/forms/:form_id/versions/:form_version` can be cached indefinitely by consumers (e.g. forms-runner, CDNs), significantly reducing load on the API and improving latency for form rendering.
- Submissions linked to form versions. Each submission can explicitly reference the `form_version` it was submitted against. This enables grouping submissions by version and helps people processing submissions to know exactly which questions were asked.
- Graceful publishing. Users who have already started filling in a form can continue submitting against the version they began with, even if the form creator publishes a new version in the meantime.
- Graceful archiving. When a form is archived, new users can be prevented from starting the form while users who have already started can finish and submit against the version they are on.
- Reverting to previous versions. Preserving all published versions makes it easier to implement future features allowing form creators to revert to a previous version of a form.
- Audit trail. The full history of published form versions is preserved and addressable.

### Negative

- Migration and data model complexity. Existing consumers (primarily forms-runner) will need to be updated to use the v3 API, requiring a transition period running both APIs in parallel. The `FormDocument` model (or a new model) will need to support version numbering alongside or in place of the current tag-based system (`draft`, `live`, `archived`).

