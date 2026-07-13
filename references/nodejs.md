# Node.js (Backend/API) Specific Checks

Load this in addition to `team-conventions.md` and `general-best-practices.md` for Node.js backend/API code (Express-style routes/controllers/middleware, or similar). The team's documented conventions (git, naming, folder structure, imports) still apply — this file covers the backend-specific additions.

## Async & error handling

- `async`/`await` used consistently rather than mixing callbacks and promises in the same codebase.
- Every `async` route handler/controller has error handling — either a `try`/`catch` or routed through centralized error-handling middleware. An unhandled rejection in a request handler that crashes the process, or silently swallows the error, is a Blocker ("code that breaks").
- Errors surfaced with meaningful messages/status codes, not generic 500s for everything or leaking internal stack traces/details to the client in production.

## API design

- Consistent request/response shape across endpoints (e.g. always `{ data, error }` or whatever the project's convention is) rather than ad hoc shapes per route.
- Input validation on request bodies/params/query (schema validation library, or at minimum explicit checks) before using the data — missing validation on user input is flagged under Security in `general-best-practices.md`.
- Proper HTTP status codes used (not `200` for every response including errors).

## Security (Node-specific, on top of general-best-practices.md)

- No string-built SQL/NoSQL queries or shell commands from user input (injection) — always a Blocker.
- Sensitive config (DB credentials, API keys, JWT secrets) loaded from environment variables, never hardcoded in source.
- Auth/authorization middleware actually applied to routes that need it — check that a new route wasn't added without going through the existing auth guard pattern.
- Rate limiting / basic abuse protection on public-facing endpoints, especially auth endpoints (login, password reset).
- CORS configured to the actual allowed origins, not wide open (`*`) in production code, unless the endpoint is genuinely public.

## Performance

- Blocking, synchronous operations (e.g. `fs.readFileSync`, heavy synchronous loops) on the request path in a server that needs to stay responsive under load — Blocker-level since it blocks the event loop for all other requests.
- Database queries in a loop (N+1) instead of a batched/joined query.
- Missing indexes implied by query patterns, if the code/schema is visible.

## Structure

- Route/controller/service/model layers kept separate rather than business logic embedded directly in route handlers — mirrors the frontend's "pages stay thin, delegate to hooks" principle (`team-conventions.md` §8).
- Shared logic extracted into `utils`/`services` rather than duplicated across route files.
