# React + Next.js Specific Checks

Load this in addition to `team-conventions.md` and `general-best-practices.md` when the code uses Next.js (app-router markers like `app/`, `page.tsx`, `layout.tsx`, `"use client"`, `next/*` imports).

## Server vs. Client components

- `"use client"` should be as far down the tree as possible — don't mark a whole page/layout client-side just because one small leaf component needs interactivity or a hook. **Blocker-level** if it forces a large subtree to lose server-rendering benefits for no real reason (this is a re-render/performance issue).
- Data fetching for anything that can happen on the server should happen in a Server Component, not fetched client-side with `useEffect` after the page has already rendered (avoids request waterfalls). Flag client-side fetch-on-mount patterns that duplicate what a Server Component could do directly.
- Passing non-serializable values (functions, class instances) from a Server Component into a Client Component as props will break — check for this.

## Routing & structure

- Page folders follow the team's `app/(group)/page-name/page.tsx` pattern (see `team-conventions.md` §8) — route groups `(auth)`, `(dashboard)` etc. for organization without affecting the URL.
- Use `next/link` (`<Link>`) for internal navigation, not plain `<a>` tags (loses client-side navigation/prefetching).
- Use `next/image` (`<Image>`) for images to get automatic optimization, sizing, and lazy-loading — flag plain `<img>` for content images (decorative/tiny icons are more of a judgment call).
- Dynamic route params and `searchParams` should be validated/typed, not trusted as-is.

## Environment variables

- Anything intended for the client bundle must be prefixed `NEXT_PUBLIC_`. Flag any secret/API key that does **not** need client exposure but is prefixed `NEXT_PUBLIC_` — that's a Blocker-level security issue (leaks to the browser bundle).
- Server-only secrets (no `NEXT_PUBLIC_` prefix) should never be imported into a file marked `"use client"`.

## Metadata / SEO

- Pages should export `metadata` (or `generateMetadata`) rather than manually injecting `<head>` tags where the app-router metadata API is available.

## Rendering strategy

- Check that `revalidate`/caching choices (static, ISR, dynamic) make sense for the data — e.g. frequently-changing data (like a POS product grid or cart) shouldn't be statically cached without revalidation.
