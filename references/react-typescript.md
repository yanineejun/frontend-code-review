# React + TypeScript (No Next.js) Specific Checks

Load this in addition to `team-conventions.md` and `general-best-practices.md` for plain React + TypeScript projects (e.g. Vite/CRA-based, no app-router).

## TypeScript usage

- No unjustified `any` — should be a real type, `unknown` with narrowing, or a generic. Flag `any` used to silence an error rather than model the actual shape.
- Component props should have an explicit typed interface/type, not inferred implicitly or typed as loosely as `Record<string, unknown>`.
- Prefer `type`/`interface` for props consistently within the codebase (either is fine, but flag inconsistency within the same project).
- Non-null assertions (`!`) used to bypass a legitimate null-check — flag unless clearly safe and commented why.

## Component structure

- Functional components with hooks — flag legacy class components introduced in new code unless the file is already a class component being extended for a specific reason.
- Props destructured in the function signature rather than accessed via `props.x` scattered through the body (readability).
- Components follow the team's Atomic Design placement (see `team-conventions.md` §8) — a component combining multiple atoms belongs in `molecules/`, not `atoms/`, etc.

## Hooks

- Custom hooks prefixed `use` and follow the Rules of Hooks (not called conditionally, not called inside loops, not called after an early return).
- Data-fetching hooks (like the team's `useProducts.ts` example) expose a consistent shape (`{ data, loading, error }` or similar) rather than ad hoc return shapes across the codebase.
- Effects with missing cleanup where one is needed (subscriptions, timers, event listeners left un-removed on unmount).

## Routing (if using React Router or similar)

- Route-level code splitting (`React.lazy` + `Suspense`) for large route components, especially if the app has many pages, to avoid one huge initial bundle.
- Route params validated/typed rather than trusted as raw strings.
