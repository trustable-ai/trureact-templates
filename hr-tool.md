# Phase 1 - Analyse the project and define the implementation plan

Transform the current application into **Nuvolaris HR**, a professional human-resources management platform.

Do not create a new application. Reuse the current architecture, directory structure, components, design system, coding conventions, backend patterns, official service bindings, and deployment workflow. Do not replace working parts unnecessarily.

The user interface must be in Italian. The implementation must use real persisted data and must not stop at a scaffold, prototype, frontend-only flow, static placeholder, or mocked response.

## Analysis only

In this phase, analyse the project without modifying files, creating actions, changing infrastructure data, or deploying anything.

Inspect the project and identify:

- the existing frontend architecture, routes, components, styles, and state management;
- the existing backend actions and API conventions;
- the available official PostgreSQL, Redis, MongoDB, and S3 integrations;
- the current authentication and authorisation state;
- the existing test, typecheck, build, watcher, and browser-verification commands;
- reusable code and any missing capabilities.

Define the target architecture and data ownership:

- PostgreSQL: users, roles, departments, employees, reporting relationships, employment status, and employment events;
- Redis: application sessions and dashboard-statistics caching;
- S3: employee photographs and PDF CV files;
- MongoDB: structured CV profiles, CV version metadata, and manually maintained CV content.

Produce an ordered implementation checklist covering:

- database and idempotent setup;
- authentication and role enforcement;
- employee APIs and screens;
- photographs and CV files;
- structured CV profiles;
- employment events, turnover, and dashboard;
- administrator user management;
- responsive and defensive UX;
- tests, typecheck, build, runtime checks, and final browser verification.

## Execution rules for all subsequent phases

- Use only official integrations and bindings already available in the project.
- Do not invent hostnames, URLs, credentials, secrets, environment variables, or unsupported bindings.
- Treat generated `.env` files and generated action wrappers as host-owned artifacts.
- Never expose credentials, connection strings, tokens, session identifiers, internal URLs, or storage secrets in frontend code, browser storage, responses, logs, errors, or model-visible files.
- Do not run `ops ide deploy` or start another `ops ide devel`. The managed watcher owns normal live updates.
- After a coherent batch that creates one or more new OpenServerless actions, use the managed runtime redeploy tool once when required by the runtime, then continue with watcher status and real endpoint verification.
- Do not ask the user to execute technical commands. Diagnose and resolve implementation errors autonomously using the managed tools.

Finish this phase with the architecture and checklist only. Do not begin implementation yet.


---

# Phase 2 - Build the data, authentication, and authorisation foundation

Continue from the approved architecture and checklist from Phase 1.

## Idempotent relational foundation

Create or extend an idempotent PostgreSQL setup for:

- users;
- roles;
- account activation status;
- departments;
- employees;
- manager and reporting relationships;
- employment events;
- file metadata needed to associate S3 objects and CV versions with employees.

Use stable identifiers, appropriate uniqueness constraints, foreign keys, timestamps, and indexes. Setup must be safe to execute more than once and must preserve existing valid data.

## Authentication

Implement complete real authentication with:

- one-time setup of the first administrator;
- login;
- logout;
- current-session or `me` validation;
- persistent Redis-backed sessions;
- bounded and correctly enforced session expiration;
- secure password hashing;
- server-side session invalidation;
- backend protection for every protected API.

Use Redis-backed opaque sessions. Every Redis key must derive from the official application prefix. Do not implement JWT-based application authentication and do not create application secrets or edit environment files.

## Authorisation model

Implement these roles:

- **Administrator**: full access, including user administration;
- **HR Manager**: access to employee, CV, event, turnover, and dashboard capabilities, but no access to user administration.

Centralise backend authentication and role checks so frontend route protection is never the only security boundary. Every authenticated request must verify that the user still exists and is active, so a deactivated account immediately loses access even if it holds an older session.

The frontend foundation must provide:

- an Italian first-administrator setup screen;
- an Italian login screen;
- logout;
- redirect to the dashboard after login;
- redirect to login when an unauthenticated visitor opens a protected page;
- a loading state while the existing session is validated;
- safe handling of expired or invalid sessions.

Create and wire the complete authentication action set coherently. If new actions require managed redeploy, perform it once after the complete batch, never through shell commands.

## Focused verification

Before completing this phase, verify with real APIs:

- first-administrator setup can run exactly when allowed;
- duplicate first-administrator setup is rejected safely;
- login succeeds with valid credentials and fails safely otherwise;
- password hashes and session values are never returned;
- `me` validates a live session;
- logout invalidates the session;
- session expiration is enforced;
- unauthenticated protected API requests are denied;
- HR Manager access to administrator-only APIs is denied;
- an inactive account cannot create or retain a valid session.

Do not perform the complete product browser E2E yet. Record focused results and update the checklist.


---

# Phase 3 - Implement employee management and employment history

Continue from the authenticated foundation. Do not work on file uploads or structured CV storage in this phase.

## Employee domain

Implement complete employee CRUD using PostgreSQL as the authoritative source.

Each employee must support:

- first name and surname;
- email address;
- telephone number;
- unique employee ID;
- department;
- company role;
- manager;
- hire date;
- contract type;
- status: active, leaving, or terminated;
- departure date;
- departure reason;
- notes.

Record a reliable employment-event history containing at least:

- hiring;
- transfer or department/manager change;
- termination.

Employee mutations must update employee state and append the corresponding event consistently. Validate dates, identifiers, required fields, status transitions, manager references, and duplicate email or employee IDs on the backend.

## Employee interface

Implement the Italian employee section with:

- a searchable employee list;
- filters by department, company role, and status;
- sorting;
- pagination;
- loading, empty, validation, and error states;
- employee creation and editing forms;
- a complete employee profile;
- current employment information;
- employment-event history;
- clear success and failure notifications.

The profile may show an honest empty state for photograph and CV capabilities that will be implemented in Phase 4. Do not use fake files or simulated CV data.

Protect every employee and employment-event endpoint for Administrator and HR Manager roles. Unauthenticated users must receive an authentication failure, not partial data.

If this phase creates new actions, complete the coherent action and connector batch before invoking the managed redeploy once. Ordinary source changes remain owned by the watcher.

## Focused verification

Verify with recognisable real test data:

- employee creation;
- employee editing;
- duplicate and invalid input rejection;
- search, filters, sorting, and pagination;
- complete employee profile retrieval;
- hiring, transfer, and termination history;
- authentication and role enforcement on every employee endpoint;
- Italian loading, empty, success, and error states;
- deterministic React validation and the employee browser flow.

Keep the test employee available for the later file and turnover phases. Update the checklist.


---

# Phase 4 - Implement photographs, CV files, and structured CV profiles

Continue from the real employee created in Phase 3.

## Photograph and PDF storage

Use S3 only through protected backend actions to support:

- one current employee photograph;
- photograph upload, display, replacement, and deletion;
- one or more PDF CV files;
- CV upload, download or protected viewing, replacement, and deletion;
- a simple ordered CV version history.

Validate allowed MIME type, extension, and file size on both frontend and backend. Generate safe server-owned object keys. Never trust a browser-supplied bucket or unrestricted object path.

The browser must never connect directly to S3 and must never receive S3 credentials. S3 mutations belong in protected backend actions using the official binding.

## Structured CV data

Use MongoDB for an editable structured profile associated with each employee and CV version. It must contain:

- professional summary;
- skills;
- work experience;
- education;
- languages;
- date of the latest update.

Do not implement AI-based CV parsing. Structured information is entered and maintained manually.

Keep PostgreSQL employee/file references, S3 objects, and MongoDB structured records consistently associated. Design compensation or cleanup for partial failures so a failed S3 or MongoDB operation does not leave a false successful PostgreSQL record or crash the entire interface.

## CV interface

Extend the Italian employee profile with:

- photograph controls and preview;
- CV upload and protected view/download actions;
- visible CV version history;
- structured CV view and edit forms;
- file validation messages;
- recoverable service errors without blank pages or leaked internals.

If new actions are created, use the managed redeploy once after the complete file/CV action batch. Do not run deployment commands manually.

## Focused verification

Verify against the employee retained from Phase 3:

- photograph upload, display, replacement, and deletion;
- valid PDF upload, view/download, replacement, versioning, and deletion;
- unsupported and oversized file rejection;
- structured CV creation and editing;
- correct employee association across PostgreSQL, S3, and MongoDB;
- protected access for Administrator and HR Manager;
- unauthenticated rejection;
- recoverable behaviour for a simulated malformed service response without exposing secrets;
- deterministic React validation and the complete profile browser flow.

Update the checklist with real results.


---

# Phase 5 - Implement turnover, dashboard statistics, and Redis caching

Continue from the real employees and employment events created in the previous phases.

## Turnover calculations

Use PostgreSQL employment events as the authoritative source. Do not use hardcoded, random, static, or simulated figures.

Implement an HR dashboard showing:

- total employees;
- active employees;
- new hires during the selected period;
- departures during the selected period;
- turnover percentage;
- employee distribution by department;
- monthly hiring and departure trends;
- latest personnel events.

Allow the user to:

- select the analysis period;
- filter dashboard data by department.

Define the turnover formula explicitly and handle empty periods without division errors or misleading percentages.

## Redis cache

Cache expensive dashboard statistics in Redis using only keys derived from the official application prefix. Cache keys must include every dimension that changes the result, including period and department.

Employee and employment-event mutations must invalidate every affected dashboard cache entry. PostgreSQL remains authoritative: a missing or stale cache entry is rebuilt from real relational data.

Do not expose cache keys, credentials, connection details, or raw Redis diagnostics in the browser.

If new dashboard actions are created, complete them before one managed redeploy. Do not deploy ordinary edits manually.

## Focused verification

Verify with known hires, transfers, and departures:

- every dashboard total against the corresponding PostgreSQL data;
- period selection;
- department filtering;
- monthly trends;
- zero-data periods;
- cache miss followed by cache population;
- cache reuse for an identical query;
- invalidation after employee or employment-event changes;
- correct recalculation after invalidation;
- authentication and role enforcement;
- deterministic React validation and dashboard browser behaviour.

Update the checklist and retain the data required for final E2E verification.


---

# Phase 6 - Complete user administration and harden the product UX

Continue from the secure role model built in Phase 2.

## Administrator-only user management

Implement the backend and Italian administrator interface for:

- creating users;
- assigning Administrator or HR Manager roles;
- changing roles;
- deactivating accounts;
- reactivating accounts;
- clearly displaying account status;
- validating duplicate users and invalid transitions;
- understandable errors without sensitive details.

Only Administrators may open or invoke user-management pages and APIs. HR Managers must be denied server-side even if they call an endpoint directly.

Deactivation must immediately invalidate existing sessions and prevent new login. Reactivation restores login capability but must not revive old invalidated sessions.

## Complete professional UX

Unify the application into a professional, responsive, accessible Italian interface with:

- side navigation;
- dashboard;
- employees section;
- turnover section;
- administrator-only user section;
- profile menu;
- logout action;
- validated forms;
- confirmation prompts for destructive operations;
- clear notifications;
- skeletons or loading indicators;
- useful empty states;
- recoverable error states;
- responsive desktop and mobile layouts.

A malformed API response or one failed backend service must not crash the complete interface. Add defensive response handling and appropriate error boundaries or equivalent safeguards. Never show stack traces, infrastructure internals, tokens, or connection details.

The application must work in the current development environment and after deployment without hardcoded domains, hosts, ports, or URLs. The frontend must never connect directly to PostgreSQL, Redis, MongoDB, or S3.

Use the managed redeploy only if this phase introduces new actions. Let the watcher apply normal frontend and backend source edits.

## Focused verification

Verify:

- Administrator user creation, role change, deactivation, and reactivation;
- immediate loss of access for a deactivated signed-in user;
- old sessions remain invalid after reactivation;
- HR Manager cannot view or call administrator management;
- navigation visibility follows role without replacing backend enforcement;
- destructive confirmations and validation messages;
- mobile and desktop navigation;
- graceful handling of malformed and failed API responses;
- absence of blank pages and sensitive errors;
- deterministic React validation and the administrator browser flow.

Update the checklist. Do not declare the whole application complete yet.


---

# Phase 7 - Run final validation and prove the deployed application

Proceed autonomously until the complete application is verified. Fix root causes rather than suppressing failures, and do not ask the user to execute technical commands.

## Build and runtime gates

Before browser E2E:

- run the frontend typecheck;
- run the frontend production build;
- run relevant backend and application tests;
- resolve every actionable error;
- if a pending new-action batch still requires managed redeploy, invoke the managed runtime redeploy once before verification;
- inspect the authoritative managed watcher status;
- run the prescribed application checker once for the current revision;
- run deterministic React validation;
- verify real HTTP endpoints.

Do not run `ops ide deploy`, start another watcher, inspect generated ZIP files, or repeat unchanged checks in a loop.

## Final browser E2E

Use the managed browser against the real deployed application and verify:

1. initial administrator setup when the installation is uninitialised;
2. login and redirect to the dashboard;
3. denied protected pages and APIs without authentication;
4. employee creation;
5. employee editing, search, filtering, sorting, and pagination;
6. complete employee profile and employment history;
7. photograph upload, display, replacement, and deletion;
8. PDF CV upload, protected viewing, versioning, replacement, and deletion;
9. structured CV profile creation and editing;
10. employee departure recording;
11. correct turnover and dashboard updates;
12. dashboard cache invalidation after relevant mutations;
13. HR Manager user creation and permitted HR workflows;
14. denied administrator UI and APIs for the HR Manager;
15. account deactivation and immediate session rejection;
16. reactivation without restoration of the old session;
17. logout and session invalidation;
18. loading, empty, validation, error, and destructive-confirmation states;
19. unsupported and oversized file rejection;
20. graceful handling of an individual malformed backend response;
21. responsive navigation on desktop and mobile;
22. no browser-console errors;
23. no credentials, secrets, session values, stack traces, internal URLs, or connection details in browser-visible output;
24. real PostgreSQL, Redis, MongoDB, and S3 behaviour with no mocked fallback.

Use recognisable test data. Remove it only when removal does not prevent the user from inspecting the completed application.

Do not declare completion until authentication, authorisation, employee CRUD, photographs, CV files and versions, structured CV profiles, employment events, turnover, dashboard, user administration, responsive navigation, error handling, cache invalidation, session invalidation, backend protection, and all four official service integrations work together.

## Completion report

Finish with a concise report containing:

- the implemented architecture;
- the data model and ownership used in each service;
- the completed checklist;
- managed redeploys performed and why they were required;
- tests, typechecks, builds, runtime checks, and React validation executed;
- browser scenarios verified;
- any genuine remaining limitation.
