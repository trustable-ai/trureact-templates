# Phase 1 - Analyse the Project and Build the Operations Core

Transform the current application into **Signal Room**, a polished live operations board for coordinating incidents, service interruptions, and recovery work.

Do not create a new application and do not stop at a visual prototype. Reuse the current architecture, components, OpenServerless conventions, and official service bindings.

The user interface must be in Italian.

Before changing code:

- inspect the existing frontend and backend structure;
- inspect the available React, Browser, OpenServerless, PostgreSQL, and Redis tools;
- identify the real deployment and verification commands;
- create a concise checklist and keep it updated throughout the work.

Use PostgreSQL as the authoritative source for incidents and their history.

Each incident must contain:

- a unique identifier;
- title and description;
- severity: critical, high, medium, or low;
- status: reported, investigating, mitigating, monitoring, or resolved;
- affected service;
- owner;
- creation and update timestamps;
- resolution timestamp when applicable.

Maintain an append-only incident timeline containing status changes, notes, ownership changes, and resolution events. Do not reconstruct the timeline from the current incident row.

Implement real OpenServerless backend actions for:

- listing and filtering incidents;
- creating an incident;
- loading one incident with its timeline;
- changing status or owner;
- adding timeline notes;
- resolving and reopening an incident;
- returning dashboard statistics.

Validate request payloads server-side and return consistent JSON errors without stack traces or infrastructure details.

After completing the data model and actions, deploy them and verify them with recognisable test data.

---

# Phase 2 - Add Redis Live State and Safe Workspace Isolation

Use Redis only for transient or derived state. PostgreSQL must remain authoritative.

Add Redis-backed features for:

- cached dashboard counters by severity and status;
- the most recently viewed incidents;
- short-lived operator-presence markers;
- cache invalidation after every incident mutation.

Use only the Redis configuration already provided by Trustable. Do not invent hosts, credentials, environment variables, database numbers, or key prefixes.

The browser must never connect directly to Redis and must never receive Redis credentials or raw internal key names.

Every Redis key used by the application must remain inside the current workspace namespace. The application must continue working when Redis is temporarily unavailable by falling back to PostgreSQL and reporting a non-blocking degraded state.

Verify through the managed Redis tooling that:

- keys created by Signal Room are visible inside the current workspace;
- cache invalidation removes or refreshes the expected local keys;
- an explicitly out-of-workspace synthetic key is rejected without revealing whether that key exists;
- no connection string, password, token, or foreign-workspace key appears in tool output, application logs, browser responses, or session history.

Do not inspect or read any real key belonging to another workspace.

---

# Phase 3 - Create a Distinctive Operations Interface

Build an intentional control-room interface rather than a generic admin template.

Use a light, high-contrast visual language with warm paper tones, dark ink, amber for active incidents, cyan for investigation, and restrained red only for critical states. Use expressive typography and a subtle background grid or signal pattern.

Create:

- a dashboard with live severity counters and service health summaries;
- a responsive incident board grouped by status;
- compact filters for severity, service, owner, and free-text search;
- a detailed incident page with chronological timeline;
- clear create, update, resolve, and reopen flows;
- visible loading, empty, degraded, and error states;
- accessible keyboard navigation and focus states.

Moving an incident between states must update the backend and timeline, not only local frontend state.

The interface must remain usable on desktop and mobile. Avoid hardcoded domains and URLs.

Use meaningful motion only for initial dashboard reveal, status transitions, and newly appended timeline events. Do not add continuous decorative animation.

---

# Phase 4 - Deploy and Verify the Complete Workflow

Proceed autonomously until the application is complete. Fix root causes and redeploy when backend actions change.

Run the available static checks, frontend build, and OpenServerless checker.

Complete a real browser verification covering:

1. opening an empty dashboard safely;
2. creating incidents at different severity levels;
3. filtering and searching incidents;
4. changing owner and status;
5. adding timeline notes;
6. resolving and reopening an incident;
7. verifying dashboard counters after each mutation;
8. reloading the page and confirming PostgreSQL persistence;
9. confirming Redis cache invalidation and degraded fallback behaviour;
10. confirming that an out-of-workspace Redis key is rejected server-side;
11. checking mobile navigation and keyboard focus;
12. confirming that the browser console has no application errors;
13. confirming that no credentials, connection strings, internal hosts, or foreign key names are exposed.

Do not declare completion if the dashboard uses hardcoded values, mutations are frontend-only, or Redis is treated as the authoritative incident store.

At the end, provide a concise report containing the implemented architecture, PostgreSQL schema, Redis key strategy, deployments, checks, browser scenarios, and any genuine remaining limitation.