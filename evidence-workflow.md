# Phase 1 - Analyse the Project and Design the Evidence Model

Transform the current application into **Atlas Evidence Vault**, a professional case-and-evidence workspace for collecting documents, images, notes, and custody events.

Do not create a separate application and do not settle for mocked uploads or static cards. Reuse the existing architecture, design system, OpenServerless actions, and official Trustable service bindings.

The user interface must be in Italian.

Before editing:

- inspect the existing frontend and backend;
- inspect the available S3, PostgreSQL, React, Browser, and OpenServerless tools;
- identify the current deployment workflow;
- maintain a concise implementation and verification checklist.

Use PostgreSQL as the authoritative source for cases, evidence metadata, and chain-of-custody events.

A case must contain:

- case identifier;
- title and description;
- category;
- status: open, under-review, archived;
- responsible person;
- creation and update timestamps.

An evidence record must contain:

- evidence identifier;
- case identifier;
- original filename and safe object key;
- media type and byte size;
- SHA-256 checksum;
- tags and description;
- uploader name;
- upload timestamp;
- current lifecycle state.

Store every custody event as an append-only record: upload, metadata update, download, archive, restore, and deletion. Never rewrite historical custody events.

Implement real backend actions for case CRUD, evidence listing, metadata updates, custody history, and dashboard summaries. Validate all payloads server-side and use consistent redacted error responses.

Deploy and verify the database actions before starting file operations.

---

# Phase 2 - Implement the Managed S3 Evidence Workflow

Use S3 for evidence file bytes and PostgreSQL for metadata and custody history.

Use only the managed S3 connection provided by Trustable. Do not invent endpoints, buckets, regions, access keys, secret keys, environment variables, or connection names.

Before implementing uploads, verify that the managed S3 MCP exposes exactly one usable default connection for the current workspace. If Trustable declares S3 configured but no connection exists, report the provisioning error clearly instead of silently mocking storage.

Implement secure backend-mediated operations for:

- uploading a small document or image;
- listing evidence for a case;
- downloading or previewing an object;
- updating evidence metadata without replacing bytes;
- archiving and restoring evidence;
- deleting evidence with explicit confirmation.

Requirements:

- generate collision-resistant object keys scoped to the current application;
- preserve the original filename only as metadata;
- reject unsupported file types and oversized files on both frontend and backend;
- calculate and persist a SHA-256 checksum;
- never expose S3 credentials to the browser;
- never allow arbitrary bucket or object-key selection from user input;
- never delete or modify the bucket itself;
- compensate safely when S3 succeeds but PostgreSQL fails, or vice versa;
- avoid orphaned objects and metadata records.

Verify that reconnecting the ACP session or redeploying the application does not lose the managed S3 connection.

---

# Phase 3 - Build the Atlas Vault Interface

Create a distinctive editorial archive interface, not a generic dashboard.

Use a warm neutral canvas, deep navy text, vermilion evidence markers, dossier-like cards, expressive serif headings, and a subtle catalog-grid background. Keep the interface readable and professional rather than decorative.

Create:

- a case dashboard with open, review, and archived counts;
- searchable and filterable case cards;
- a case detail page with evidence gallery and custody timeline;
- drag-and-drop and file-picker upload flows;
- upload progress and validation feedback;
- image preview and safe document download;
- evidence metadata editing;
- checksum and integrity status display;
- archive, restore, and delete confirmation flows;
- loading, empty, partial-failure, and recovery states.

The interface must be responsive and keyboard accessible. Do not hardcode hosts or URLs.

A failed preview or individual object request must not crash the case page. Show a contained error and keep the remaining evidence usable.

---

# Phase 4 - Perform Real Storage and Browser Verification

Proceed autonomously until the complete workflow works with real PostgreSQL and S3 data. Redeploy whenever backend actions change.

Run the available static checks, frontend build, and OpenServerless checker.

Complete a real browser verification covering:

1. creating a case;
2. uploading a valid image and a valid document;
3. rejecting an unsupported or oversized file;
4. reloading and confirming persisted evidence metadata;
5. previewing the image;
6. downloading the document;
7. verifying the stored SHA-256 checksum;
8. editing tags and description without replacing the object;
9. inspecting the append-only custody timeline;
10. archiving and restoring evidence;
11. deleting one evidence item with confirmation;
12. confirming no orphan metadata or object remains;
13. redeploying and confirming the managed S3 connection still works;
14. confirming the browser console has no application errors;
15. confirming that no access key, secret key, connection string, internal storage URL, or stack trace appears in browser responses, logs, tool output, or session history.

Do not declare completion if uploads are mocked, evidence disappears after refresh, the frontend talks directly to S3 with credentials, or the managed S3 connection is unavailable.

At the end, provide a concise report containing the implemented architecture, PostgreSQL schema, S3 object-key strategy, consistency handling, deployments, checks, browser scenarios, and any genuine remaining limitation.