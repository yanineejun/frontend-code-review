# Team Conventions (Mandatory — Apply Regardless of Stack)

Source: team's "Frontend convention" doc + Aug 2025 "Setup Policy" brainstorm session (dept: IT, speaker: Jam). Why this matters, per the team: consistency (no one-off personal styles), maintainability (easier debugging), scalability (avoids chaos as team/features grow), and knowledge sharing (faster onboarding).

## 1. Git — Branch naming

Format: `<type>/<ticket>-<short-description>`

| Type       | Example                           |
| ---------- | --------------------------------- |
| `feat/`    | `feat/T-456-user-authentication`  |
| `bugfix/`  | `bugfix/T-789-fix-header-styling` |
| `hotfix/`  | `hotfix/T-321-security-patch`     |
| `release/` | `release/v2.0.1`                  |
| `docs/`    | `docs/T-654-update-readme`        |

Reference: https://graphite.dev/guides/git-branch-naming-conventions

**Check for:** correct prefix for the change type, ticket number present (except `release/`), kebab-case description, description is short and matches the actual change.

## 2. Git — Commit messages

Follow **Conventional Commits**: https://www.conventionalcommits.org/en/v1.0.0/

Format: `<type>(<optional scope>): <description>` — e.g. `fix(checkout): correct VAT rounding`, `feat(auth): add SSO login`.

**Check for:** valid type (`feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, etc.), imperative mood, description actually describes the change (not "wip" / "fix stuff").

## 3. Git — Merge

**Always squash commits when merging** a feature/bugfix/hotfix branch — don't carry a messy WIP commit history into the target branch.

Merge request naming is **not documented** by the team yet — default to mirroring branch/ticket naming (`T-XXX: short description`) and flag it as unconfirmed if the user pushes on the exact format.

_(Note: a "Way 2" git strategy was floated in the brainstorm — single `main`, feature branches cut from `main`, merged back into `main` with a tag — plus a tag-based release idea: tag `test1.2` → build/deploy to test/UAT, tag `v2.3` → build/deploy to prod. This was pitched as an improvement idea, not confirmed policy — only hold code to it if the user confirms their project uses it.)_

## 4. Naming conventions

**Imported variables for static assets → PascalCase (UpperCamelCase).** Treat an imported image/asset like a component or constant:

```ts
import SCB from "./scb.png";
import KBank from "./kbank.png";
```

Why: matches React's convention for components/constants, easy to distinguish from normal functions/variables, plays well with TypeScript autocomplete.

**Actual file names → lowercase, hyphens if needed** (not PascalCase): `scb.png`, `kbank.png`, `bangkok.png`. Why: avoids case-sensitivity bugs on Linux servers, matches general web asset naming convention.

So the split is intentional: the _file on disk_ is lowercase, the _variable you import it into_ is PascalCase.

**Check for:** any imported static asset bound to a camelCase or lowercase variable name; any file on disk using PascalCase, spaces, or uppercase.

## 5. Formatter — Prettier + ESLint

Format automatically, on save. VS Code setup (`cmd+shift+p` / `ctrl+shift+p` → "Open User Settings (JSON)"):

```json
{
  // Import sorting
  "editor.codeActionsOnSave": {
    "source.sortImports": "explicit"
  },
  // Format on save
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true
}
```

**Check for:** code that isn't Prettier-formatted (inconsistent spacing/quotes/semicolons), ESLint violations, evidence formatting was done by hand instead of tooling.

## 6. Import sorting & grouping

Setup: `npm install eslint eslint-plugin-import --save-dev`. Reference: https://dev.to/receter/automatic-import-sorting-in-vscode-275m

Imports are sorted in groups, **in this order**, alphabetically within each group:

1. **External libraries** (from `node_modules`) — e.g. `react`, `@headlessui/react`, `@heroicons/react`
2. **Internal/alias imports** (your own code) — paths starting with your alias (`@/...`) or relative to `src`
3. **Relative imports** (`./` or `../`) — closest to farthest, alphabetically
4. **Style/CSS imports** (if applicable) — usually last

**Prefer barrel imports** — re-export through an `index.ts` per folder (`atoms/index.ts`, `hooks/index.ts`, etc.) rather than deep-importing individual files across the app.

**Check for:** groups out of order, imports not alphabetized within a group, deep-imports bypassing an available barrel file, style imports not placed last.

## 7. Table text alignment (applies to any component rendering tabular data)

Rule of thumb:

```
Can you add/subtract/compare it?  → Right align
Does it read as words/a sentence? → Left align
Do you only "look" at it, not read it? → Center align
```

**Left align** — "read left to right." Use for data read as a narrative or with variable length:

- General text — names, descriptions, labels
- ID / Code — PO No., SKU, product codes
- Dates — read left to right

**Right align** — "digits line up on the right." Use for data that's compared or added/subtracted, because right-aligning lines up the ones/tens/hundreds place so the eye can compare values instantly:

- Any numeric quantity — QTY, counts, weight
- Currency / price
- Percentages
- Calculated fields

**Center align** — use the least. For things that are only _looked at_, not read, and have a fixed size:

- Icon / action buttons (✏️ 🗑)
- Checkbox / toggle
- Status dot / short badge
- The _header_ of an input field where the input itself is centered (but numbers typed _inside_ the input should still be right-aligned)

**Anti-patterns to flag:**

| Wrong                                    | Right                                        | Why                                      |
| ---------------------------------------- | -------------------------------------------- | ---------------------------------------- |
| Centering numbers                        | Right-align numbers                          | Can't compare values at a glance         |
| Right-aligning names/text                | Left-align names/text                        | Harder to read                           |
| Header alignment ≠ content alignment     | Keep header and content aligned the same way | Looks misaligned/broken                  |
| Centering every column "for cleanliness" | Apply the rule per column                    | Looks tidy but is harder to actually use |

**Check for:** numeric/currency columns not right-aligned, text/name columns not left-aligned, icon-only columns not centered, header alignment mismatched with its column's content alignment.

## 8. Folder structure — Atomic Design + app-router

General app structure:

```
src/
├── app/                        # App routes / pages (Next.js app router style)
│   ├── (auth)/                 # Route group
│   │   ├── login-form/         # Page folder (kebab-case)
│   │   │   ├── page.tsx
│   │   │   ├── components/     # Page-local components
│   │   │   ├── hooks/          # Page-local hooks
│   │   │   └── index.ts
│   │   └── register/...
│   └── (dashboard)/
│       ├── home/
│       └── order-history/
│
├── components/                 # Global reusable components (Atomic Design)
│   ├── atoms/
│   ├── molecules/
│   ├── organisms/
│   ├── templates/
│   └── index.ts
│
├── hooks/                      # Global reusable hooks
├── constants/                  # Global constants/config
├── utils/                      # Helper functions
└── styles/                     # Global styles (globals.css)
```

Atomic Design mapping (design → code), using the team's POS project as the worked example:

| Level         | Examples                                                                                                                                             |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Atoms**     | Button, Icon, Input, Label, Text (Price/Title), Avatar                                                                                               |
| **Molecules** | SearchBar (Input + Icon), ProductCard (Image + Title + Code + Price + Button), DiscountInput (Dropdown + TextInput), PriceSummaryRow (Label + Value) |
| **Organisms** | ProductGrid (list of ProductCards), CheckoutSummary (price/VAT/discount/total/actions), HeaderBar (logo/date/time/avatar), SidebarMenu               |
| **Templates** | CheckoutTemplate (layout: Header + ProductGrid + CheckoutSummary)                                                                                    |
| **Pages**     | CheckoutPage (uses CheckoutTemplate + data hooks)                                                                                                    |

Each component gets its own folder with the component file + a barrel `index.ts`:

```
atoms/Button/Button.tsx
atoms/Button/index.ts
molecules/ProductCard/ProductCard.tsx
molecules/ProductCard/index.ts
```

Data-fetching hooks live in `hooks/` (e.g. `useProducts.ts`) and are consumed by page components, which pass data down into templates/organisms as props — pages themselves stay thin (fetch via hook → render template).

**Check for:** a component placed at the wrong Atomic Design level (e.g. a composite component sitting in `atoms/`), missing barrel `index.ts`, page components containing heavy logic instead of delegating to hooks/templates, business logic embedded directly in a `page.tsx` instead of organisms/hooks.

## 9. Open item flagged by the team (not yet a rule)

The team's doc notes, as a "to set" item: **validation for numeric input fields** — e.g. deciding max digit length allowed. There is no finalized rule yet. If you see a numeric input with no length/pattern validation, flag it as a 🟡 suggestion ("team hasn't finalized numeric input validation rules yet, but this field has none — worth adding"), not a blocker.
