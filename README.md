<div align="center">
  <img src="docs/logo.png" alt="Monolith Industries" width="120">
  <h1>MONOLITH INDUSTRIES</h1>
  <p><strong>Highly-opinionated, enterprise-grade Next.js infrastructure for one person.</strong></p>
  <p>
    <a href="#quick-start">Get Started</a> &middot;
    <a href="#whats-included">What's Included</a> &middot;
    <a href="#customization">Customization</a>
  </p>
</div>

---

## What's Included

| Layer                | Stack                                                                  | Status |
| -------------------- | ---------------------------------------------------------------------- | ------ |
| **Auth**             | Supabase Auth, magic link/OTP, signup + login pages, `useOtpFlow` hook | Ready  |
| **Database**         | Supabase Postgres, Drizzle ORM, empty schema                           | Ready  |
| **Background Jobs**  | pg-boss worker scaffold                                                | Ready  |
| **Error Monitoring** | Sentry client/server/edge, session replay, `/monitoring` tunnel        | Ready  |
| **Analytics**        | Vercel Analytics + Speed Insights                                      | Ready  |
| **Design System**    | Tailwind v4 + shadcn/ui, zinc + indigo tokens                          | Ready  |
| **Tooling**          | ESLint, Prettier, Husky, lint-staged, madge                            | Ready  |
| **E2E Tests**        | Playwright auth flows against real local Supabase                      | Ready  |

## Architecture

```
┌─────────────────┐     ┌──────────────────────┐
│  Vercel          │     │  Railway (optional)   │
│  Next.js App     │     │  Worker (pg-boss)     │
│                  │     │  Background jobs      │
│  Dashboard (CSR) │     │                       │
│  API Routes      │     └──────────┬────────────┘
└────────┬─────────┘                │
         │                          │
         └──────────┬───────────────┘
                    │
         ┌──────────▼────────────┐
         │  Supabase Cloud       │
         │  - Postgres (Drizzle) │
         │  - Auth (magic link)  │
         │  - Storage (media)    │
         └───────────────────────┘
```

## Quick Start

**Prerequisites:** Node.js 24 LTS (`.nvmrc` included), Docker Desktop

```bash
git clone https://github.com/josheche/monolith-industries.git
cd monolith-industries
npm install
cp .env.example .env.local

npx supabase start          # start local Postgres, Auth, Storage, Mailpit
npx supabase status          # copy URLs + keys into .env.local

npm run dev                  # http://localhost:3000
npm run worker               # optional: background job worker
```

| Service          | URL                    |
| ---------------- | ---------------------- |
| App              | http://localhost:3000  |
| Supabase Studio  | http://127.0.0.1:54323 |
| Mailpit (emails) | http://127.0.0.1:54324 |
| Supabase API     | http://127.0.0.1:54321 |

## Environment Variables

**Core** (`.env.local` for dev, Vercel for production):

```env
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
DATABASE_URL=
```

**Sentry** (Vercel only):

```env
NEXT_PUBLIC_SENTRY_DSN=
SENTRY_ORG=
SENTRY_PROJECT=
SENTRY_AUTH_TOKEN=
```

## Scripts

```bash
npm run dev            # Next.js dev server (Turbopack)
npm run worker         # pg-boss background worker
npm run build          # production build
npm run lint           # ESLint
npm run format         # Prettier
npm test               # Vitest
npm run check:circular # madge circular dep check
npx playwright test    # E2E auth flow tests
```

## Database

Schema starts empty at `src/db/schema.ts`. Add your tables:

```bash
npx drizzle-kit generate   # create migration
npx drizzle-kit migrate    # apply migration
```

## E2E Tests

```bash
cp .env.test.example .env.test   # fill from `npx supabase status`
npx playwright test              # requires local Supabase + dev server
```

## Tooling

Every commit runs through:

```
git commit → Husky pre-commit
  ├── lint-staged (eslint --fix + prettier --write)
  └── madge --circular
```

**Prettier:** no semi, single quotes, trailing commas, 100 char width
**ESLint:** flat config, Next.js Core Web Vitals + TypeScript
**TypeScript:** strict mode, `@/*` path alias

## Customization

1. **Brand** — replace "Monolith Industries" in `page.tsx`, auth pages, dashboard layout
2. **Accent color** — swap indigo values in `globals.css` (`:root` and `.dark`)
3. **Fonts** — change in `layout.tsx` via `next/font`
4. **Domain tables** — add to `src/db/schema.ts`, generate + migrate
5. **API routes** — add under `src/app/api/`
6. **Background jobs** — add in `src/worker/jobs/`, register in `src/worker/index.ts`

## Deployment

**Vercel** — auto-deploys on push to `main`
**Railway** (worker, optional) — start command: `npm run worker`
**Supabase** — create cloud project, run migrations, configure SMTP (Resend)

---

<div align="center">
  <sub>Next.js 16 · TypeScript · Tailwind v4 · shadcn/ui · Drizzle · Supabase · pg-boss · Sentry · Vercel Analytics · Vitest · Playwright</sub>
</div>
