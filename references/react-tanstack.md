# React + TanStack Specific Checks

Load this in addition to `team-conventions.md` and `general-best-practices.md` when the code uses TanStack Query, Router, and/or Table (`@tanstack/react-query`, `@tanstack/react-router`, `@tanstack/react-table`, `useQuery`, `useMutation`, etc.).

## TanStack Query

- Server/remote data fetched via `useQuery`/`useMutation`, **not** via `useEffect` + manual `fetch` + local `useState` — this is exactly the "state management" Blocker category in `general-best-practices.md` (server data belongs in the query cache, not duplicated into component state).
- Query keys are structured and consistent (e.g. `['products', filters]`) — not a bare string that collides across features, and not missing the variables that should invalidate/refetch the query.
- Mutations invalidate (`queryClient.invalidateQueries`) or optimistically update the relevant query keys on success — flag a mutation that changes server data but never invalidates/refetches the affected query.
- `staleTime`/`gcTime` (or `cacheTime`) set deliberately for data that doesn't need to refetch aggressively (e.g. product catalogs), rather than leaving defaults everywhere without thought — minor, suggestion-level.
- Loading/error states from `useQuery` are actually handled in the UI (not just the happy path).
- Avoid manually storing `useQuery` data into local `useState` "to make it easier to use" — read directly from the query result; copying it into state creates a stale-data bug.

## TanStack Router (if used)

- Route params/search params validated (TanStack Router supports typed/validated search params) rather than read as raw untyped values.
- Data loaders used for route-level data fetching where appropriate, instead of fetching inside the component after render (avoids waterfalls, same principle as Next.js server components).

## TanStack Table (if used)

- Column definitions memoized (`useMemo`) rather than re-created every render — re-creating columns/data references on every render defeats the table's internal memoization and causes full re-renders (Blocker-level re-render issue).
- Large datasets use row virtualization instead of rendering every row into the DOM.
- Column alignment for numeric/currency columns follows the team's table alignment convention (`team-conventions.md` §7) — right-align comparable numbers, left-align text/IDs, center only icons/actions.
