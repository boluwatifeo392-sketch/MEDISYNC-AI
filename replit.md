# Workspace

## Overview

pnpm workspace monorepo using TypeScript. Each package manages its own dependencies.

## Stack

- **Monorepo tool**: pnpm workspaces
- **Node.js version**: 24
- **Package manager**: pnpm
- **TypeScript version**: 5.9
- **API framework**: Express 5
- **Database**: PostgreSQL + Drizzle ORM
- **Validation**: Zod (`zod/v4`), `drizzle-zod`
- **API codegen**: Orval (from OpenAPI spec)
- **Build**: esbuild (CJS bundle)
- **AI Integration**: OpenAI via Replit AI Integrations (drug interaction checking, prescription analysis)

## Applications

### MediSync AI (`artifacts/medisync-ai`)
A medication safety and drug interaction tracking web app.
- **Pages**: Dashboard, Medications, Drug Interaction Checker, Prescription Scanner, Alerts
- **AI features**: Drug interaction analysis, prescription text scanning (powered by OpenAI gpt-5-mini)
- **Data**: 5 sample medications seeded, 3 sample alerts seeded

## Structure

```text
artifacts-monorepo/
├── artifacts/              # Deployable applications
│   ├── api-server/         # Express API server
│   └── medisync-ai/        # MediSync AI React frontend
├── lib/                    # Shared libraries
│   ├── api-spec/           # OpenAPI spec + Orval codegen config
│   ├── api-client-react/   # Generated React Query hooks
│   ├── api-zod/            # Generated Zod schemas from OpenAPI
│   └── db/                 # Drizzle ORM schema + DB connection
├── scripts/                # Utility scripts
├── pnpm-workspace.yaml
├── tsconfig.base.json
├── tsconfig.json
└── package.json
```

## Database Schema

- `medications` — tracks user medications (name, dosage, frequency, timeOfDay, startDate, condition, etc.)
- `alerts` — medication alerts (interaction warnings, reminders, general info)

## API Routes

All routes prefixed with `/api`:

- `GET /api/medications` — list all medications
- `POST /api/medications` — add medication
- `PUT /api/medications/:id` — update medication
- `DELETE /api/medications/:id` — delete medication
- `POST /api/interactions/check` — AI-powered drug interaction check
- `POST /api/prescriptions/analyze` — AI-powered prescription text analysis
- `GET /api/alerts` — list all alerts
- `POST /api/alerts/:id/dismiss` — dismiss an alert

## TypeScript & Composite Projects

Every package extends `tsconfig.base.json` which sets `composite: true`. The root `tsconfig.json` lists all packages as project references.

- **Always typecheck from the root** — run `pnpm run typecheck`
- **`emitDeclarationOnly`** — only emit `.d.ts` files during typecheck
- **Project references** — when package A depends on package B, A's `tsconfig.json` must list B in its `references` array

## Root Scripts

- `pnpm run build` — runs `typecheck` first, then recursively runs `build` in all packages that define it
- `pnpm run typecheck` — runs `tsc --build --emitDeclarationOnly` using project references
