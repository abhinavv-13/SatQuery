# UI-Only Project Rules

This project is **frontend/UI only**. AI must build UI and nothing else.

## Scope

**Allowed**
- Components, pages, layouts, routing (client-side only)
- Styling (CSS / Tailwind / design tokens), theming, dark mode
- Animations, transitions, micro-interactions
- Responsive + accessible markup (semantic HTML, ARIA, keyboard support)
- Local UI state (open/close, tabs, form input state, validation display)
- Mock/static data to make screens look real

**Not allowed — never create or modify**
- Backend code: servers, API routes, controllers, middleware
- Database schemas, ORM models, migrations, queries
- Auth logic, sessions, JWT, OAuth flows
- Server actions, serverless functions, cron jobs, webhooks
- Env secrets, API keys, real third-party integrations
- Docker, CI/CD, deployment or infra config

## Data Handling

- Use hardcoded mock data in a separate file: `src/mocks/` (or `mocks/`)
- Never call a real endpoint. No `fetch`/`axios` to live URLs.
- If a screen needs data or an action, expose it as **props or callbacks** (e.g. `onSubmit`, `data`, `isLoading`) so a backend can be plugged in later.
- Mark integration points with a comment: `// TODO(backend): <what is needed>`
- Define TypeScript types/interfaces for expected data shapes, but do not implement the data layer.

## When a Request Needs Backend

Don't build it. Do this instead:
1. Build the UI part fully (including loading, empty, error, success states).
2. Mock the data/response.
3. Add a `TODO(backend)` note.
4. Tell me in one line what backend piece is missing.

## Code Rules

- Keep components small, reusable, and single-purpose.
- One component per file; colocate its styles/types.
- No business logic in components beyond presentation.
- No new dependencies unless they are UI-related (icons, UI kit, animation). Ask first if unsure.
- Match the existing stack and design system. Don't switch frameworks or libraries.

## UI Quality Checklist

Every screen should have:
- [ ] Responsive (mobile → desktop)
- [ ] Loading, empty, and error states
- [ ] Hover / focus / disabled states
- [ ] Accessible (labels, contrast, keyboard nav)
- [ ] Consistent spacing, typography, and colors

## Response Style

- Be direct. Skip long explanations.
- Output code first, notes only if needed.
- If something is ambiguous, pick a sensible default, state it in one line, and continue.

## Stack (fill in)

- Framework: Next.js 16 (App Router)
- Styling: Tailwind CSS v4
- UI library: shadcn/ui (base-nova style)
- Icons: lucide-react