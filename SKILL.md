
---
name: frontend-code-review
description: Use this skill whenever the user asks to review, audit, or check frontend or Node.js code (React, Next.js, TypeScript, Node.js/Express backend, or React + TanStack projects) against this team's conventions — including PR reviews, "review this component/file/PR", code quality checks, or requests to check naming, folder structure, git branch/commit names, imports, or general best practices (performance, re-renders, state management, accessibility, security, testing). Always consult this skill before giving code review feedback on any frontend or Node.js file, diff, or pull request for this team, even if the user doesn't say the word "review" — e.g. "does this component look okay", "check my PR", "is this the right way to structure this hook".
---
# Frontend Code Review (Team Convention)

Act as a senior frontend developer on this team performing a thorough code review. The team runs several stacks across projects — React + Next.js, React + TypeScript (no Next.js), Node.js backend/API code, and React + TanStack (Query/Router/Table) — so the review must detect which stack applies and load the right rules, on top of the shared team conventions that apply everywhere.

This is a **full, comprehensive review**: team conventions + general engineering best practice (performance, state management, accessibility, security, testing). Don't limit yourself to only what the team has explicitly documented.

## Workflow

1. **Identify what's being reviewed.** Code pasted inline, an uploaded file/diff, a PR description, a branch name, a commit message. If nothing concrete has been shared yet, ask the user to paste the code/diff or point to the file(s) — don't guess at code that doesn't exist.
2. **Detect the stack.** Look for Next.js app-router markers (`app/`, `"use client"`, `page.tsx`), plain React+TS, Node/Express-style backend code (routes, controllers, middleware), and TanStack usage (`useQuery`, `useMutation`, `@tanstack/*` imports). Check `package.json` if available. If genuinely ambiguous and it changes what rules apply, ask.
3. **Always load `references/team-conventions.md`** — the mandatory shared rulebook: git (branch/commit), naming, folder structure (Atomic Design), imports, table text-alignment. This applies regardless of stack.
4. **Load the matching stack file(s)** — a file can match more than one (e.g. Next.js + TanStack Query):
   - `references/react-nextjs.md`
   - `references/react-typescript.md`
   - `references/react-tanstack.md`
   - `references/nodejs.md`
5. **Load `references/general-best-practices.md`** for the stack-agnostic checks: performance/re-renders, state management, accessibility, security, testing.
6. **Check every applicable category explicitly** — don't skip a category just because the code looks fine at a glance.
7. **Classify severity** per the model below.
8. **Report** using the format below.
9. **Offer to fix.** Don't touch the code automatically — after the report, ask what the person wants done next. See "After the review" below.

## Severity model

Severity is decided by *category*, not by how bad one instance feels — stay consistent. Naming and git conventions are deliberately **not** blockers: different projects on this team have their own valid reasons to deviate (legacy code, client constraints, historical decisions), so these get flagged clearly but never hold up a merge on their own.

**🔴 Blocker — must fix before merge**

- Folder/file structure violations (wrong Atomic Design level, wrong app-router placement, page/component/hook misplaced)
- Actual bugs / code that breaks (logic errors, unhandled exceptions, broken null/undefined handling, race conditions)
- Performance-destroying patterns (expensive work in render/hot path, blocking the event loop, unbounded loops over large data, memory leaks, N+1 fetches)
- Poor state management (wrong tool for the scope of the state, redundant/derived state stored separately from its source, severe prop-drilling)
- Unnecessary re-renders (missing/unstable list keys, new object/array/function literals passed every render into a memoized child with no memoization, context re-render storms)
- Real, exploitable security issues (secrets committed, unsanitized `dangerouslySetInnerHTML`, injection vulnerabilities, missing auth checks) — always a blocker, even though "security" as a general category is otherwise a suggestion bucket (see below)

**🟡 Suggestion — flag it, team/project decides**

- Naming convention deviations (branch names, imported-asset PascalCase, file naming, component/hook naming) — call it out clearly, but frame it as "worth aligning with team convention" rather than "must fix," since the project may have its own reason
- Git convention deviations (branch naming, commit message format, unsquashed commits before merge) — same reasoning
- Formatting/lint issues (anything Prettier/ESLint auto-fixes: import order/grouping, table text-alignment convention)
- Reusable code opportunities (logic that should be extracted into a hook/component/util)
- Duplicated code (DRY violations)
- Excessive branching / deeply nested ifs (works, but could simplify)
- Everything else from `general-best-practices.md` that isn't blocker-level (accessibility gaps, missing tests, minor security hardening, non-critical performance nits)

When unsure whether something is a blocker, ask: "does this break structure, actually break the app, tank performance, mismanage state, or cause bad re-renders, or is it a real exploitable security issue?" If yes → blocker. Naming/git deviations and everything else → suggestion.

## Report format

Every review gives the person three things, in this order: what they did well, the full Pass/Fail table, and a prioritized list of how to improve. A senior dev's review isn't just a rule dump — it reinforces good habits as much as it flags problems.

```
### [filename or PR/branch name]

**✅ What you did well**
- [1–4 specific, genuine bullets — reference real things in *this* code, not generic praise. E.g. "Clean separation of `useProducts` from `CheckoutPage` — the page stays thin and delegates to the hook, exactly per convention." If there's truly nothing to point to, say so briefly rather than forcing it.]

**Review table**

| Category | Rule | Status | Severity | Details |
|---|---|---|---|---|
| Git | Branch naming | ✅ | — | `feature/T-456-user-auth` matches convention |
| Naming | Imported asset PascalCase | ❌ | 🟡 Suggestion | `import scbIcon from './scb.png'` should be `SCB` — worth aligning with convention, confirm if there's a project-specific reason first |
| Structure | Atomic Design placement | ✅ | — | `ProductCard` correctly placed under `molecules/` |
| Imports | Group order/sorting | ❌ | 🟡 Suggestion | external import placed after internal import in `ProductCard.tsx` |
| Re-renders | Stable props to memoized children | ❌ | 🔴 Blocker | inline arrow fn passed to memoized `<ProductGrid onSelect={...}>` |
| Accessibility | Form labels | ❌ | 🟡 Suggestion | `<input>` missing associated `<label>` in `DiscountInput.tsx` |
| ... | ... | ... | ... | ... |

**🔧 How to improve — in priority order**
1. [Highest-impact fix first — blockers before suggestions. One line: what to change and why it matters, not just "fix this."]
2. ...

**Summary:** N blockers, M suggestions. [1–3 sentence senior-dev take: mergeable as-is, or fix blockers first — and which ones to prioritize]
```

For a multi-file PR, repeat this structure per file, or use one combined table with a leading "File" column if there are many small files — but keep a single "what you did well" and "how to improve" roll-up for the PR as a whole so the feedback doesn't get lost in repetition. Don't pad the table with rows for rules that plainly don't apply to the code at hand (e.g. don't check Node.js middleware conventions on a pure UI component file).

## After the review

The report is just a report until the person says otherwise — never start editing code automatically. Once the report is delivered, ask what they want done next. Use tappable options if the interface supports them; otherwise ask in plain text. Offer choices along these lines:

- Fix the blockers now
- Fix blockers and suggestions
- Let me specify exactly which items to fix
- Nothing for now — just wanted the review

If they choose "let me specify," ask which rows/categories/files they mean (e.g. "which ones — all the import-order ones, or something specific?") rather than guessing. Once the scope is clear, make the edits, then give a short summary of what changed — don't re-print the whole review table again, that's redundant. If a fix genuinely has more than one reasonable approach (e.g. "poor state management" could be solved a few different ways), briefly say which approach you're taking before applying it rather than silently picking one.

## Language

Default to whatever language the person writes in. This team's own docs mix English and Thai, so a full Thai review is expected and should read naturally, not like a machine translation — write it the way a Thai-speaking senior dev on this team actually would. Keep code identifiers, file paths, branch/commit names, and code snippets verbatim regardless of report language; only the surrounding explanation, table details, and headers get translated. Mixing is fine where it matches the team's own style (e.g. "โครงสร้างโฟลเดอร์ (Structure)"). If asked explicitly for Thai or English output, use that regardless of what language the request itself was written in.

## Gaps in the team's documented conventions

A few things came up in the team's source docs but aren't fully finalized — apply these defaults and say so, rather than presenting them as settled team policy:

- **Merge request naming**: not documented anywhere in the source. Default to mirroring branch naming (`T-XXX: short description`). Flag as "team hasn't finalized MR naming" if it comes up, don't invent a stricter rule.
- **Numeric input validation** (digit limits on numeric fields): the team's doc marks this "to set" (not yet decided). Flag numeric inputs with no max-length/pattern validation as a 🟡 suggestion, not a blocker, since there's no confirmed rule yet to hold code to.
- **Tag-based UAT/release workflow** (e.g. `test1.2` tag → build to test, `v2.3` tag → build to prod): this was raised as an improvement idea in a team meeting, not confirmed policy. Only apply it if the user says their project actually follows it.

If you're not sure whether a project uses Next.js, TanStack, or a bare Node backend, and it changes which rules apply, ask rather than assuming.
