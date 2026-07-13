# General Best Practices (Stack-Agnostic)

Not documented by the team explicitly, but part of a "full comprehensive review" — apply standard industry practice. Remember: performance/re-render/state-management issues here are 🔴 Blockers per the severity model; accessibility, testing gaps, and minor security hardening are 🟡 Suggestions unless a finding is a real exploitable vulnerability (always a Blocker).

## Performance (Blocker-level when severe)

- Expensive computation, sorting, or filtering happening directly in the render body on every render instead of being memoized (`useMemo`) or moved out.
- Heavy work blocking the main thread (large synchronous loops, unthrottled scroll/resize handlers).
- Missing pagination/virtualization on large lists (rendering thousands of DOM nodes at once).
- Fetching more data than needed, or re-fetching on every render instead of caching.
- N+1 request patterns (looping and firing a request per item instead of batching).
- Large bundle impact from importing an entire library for one function (e.g. importing all of `lodash` instead of `lodash/debounce`), unused imports left in.
- Images not sized/optimized/lazy-loaded where the framework provides a mechanism for it.

## Re-renders (Blocker-level)

- New object/array/function literals created inline in JSX props on every render, passed into a child wrapped in `React.memo` — defeats the memoization.
- Missing or unstable `key` props in lists (using array index as key when list order/content can change).
- Context value objects re-created every render, causing every consumer to re-render even when nothing relevant changed — split contexts or memoize the value.
- `useEffect`/`useCallback`/`useMemo` dependency arrays that are wrong (missing deps causing stale closures, or over-broad deps causing needless re-runs).
- Component subtree re-rendering entirely when only a small leaf actually changed — consider component composition or memoization boundaries.

Don't flag `useMemo`/`useCallback`/`React.memo` used _everywhere_ as automatically good — over-memoizing trivial values/functions is itself a code smell (adds complexity/overhead for no benefit). Flag it as a 🟡 suggestion to simplify if memoization clearly isn't needed.

## State management (Blocker-level)

- Using global state (context/store) for state that's only needed locally in one component/subtree.
- Using local `useState` for state that's actually shared/derived across distant parts of the tree (severe prop-drilling through many layers instead of context/store/composition).
- Storing derived state separately instead of computing it from its source (leads to state going out of sync).
- Server/remote data kept in local component state and manually synced, instead of using a data-fetching layer (e.g. TanStack Query) that handles caching/invalidation.
- Redundant state: two state variables that always change together and could be one.

## Accessibility (Suggestion-level, unless it fully blocks a user)

- Form inputs without an associated `<label>` (or `aria-label`/`aria-labelledby`).
- Interactive elements built on non-interactive tags (`<div onClick>` instead of `<button>`) without keyboard support (`tabIndex`, `onKeyDown`, appropriate `role`).
- Images without meaningful `alt` text (or missing `alt=""` for purely decorative images).
- Color-only signaling of state (e.g. red/green with no icon or text) with no non-color cue.
- Poor focus management in modals/dialogs (focus not trapped or not returned on close).
- Insufficient color contrast for text.

## Security (Suggestion-level for hardening; Blocker if exploitable — see below)

Always a 🔴 Blocker regardless of category, because these are exploitable:

- Secrets, API keys, or credentials committed to the repo or exposed to the client bundle (e.g. a non-`NEXT_PUBLIC_`-intended secret leaking client-side).
- Unsanitized `dangerouslySetInnerHTML` / `innerHTML` rendering user-controlled content (XSS).
- String-concatenated queries/commands built from user input (SQL/command injection) in Node backend code.
- Missing authentication/authorization checks on a route or API handler that should have one.
- Sensitive data logged in plaintext (tokens, passwords, PII).

🟡 Suggestion-level hardening:

- Missing input validation/sanitization on forms even where not directly exploitable yet.
- Overly permissive CORS configuration.
- Dependencies with known vulnerabilities (flag if visible from `package.json`, suggest `npm audit`).
- Sensitive routes/endpoints with no rate limiting.

## Testing (Suggestion-level)

- No tests for non-trivial hooks, utils, or business logic (pure functions especially — cheap to test, should be tested).
- Tests that assert on implementation details (internal state, private methods) instead of observable behavior/output.
- Missing edge-case coverage (empty states, error states, loading states) for components that clearly branch on those states in the code.
- Snapshot tests used as the _only_ test for logic-heavy components (low signal, brittle).

## Code quality / general (Suggestion-level, per severity model)

- Duplicated logic across files that should be a shared hook/util/component.
- Deeply nested conditionals/callbacks (`if` inside `if` inside `if`) that could be flattened with early returns, guard clauses, or extraction.
- Dead code, commented-out code left in, unused variables/imports.
- Magic numbers/strings that should be named constants.
- Functions/components doing too many unrelated things (single-responsibility violations) — suggest splitting.
