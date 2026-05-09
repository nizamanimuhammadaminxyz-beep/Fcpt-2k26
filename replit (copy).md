# Section B Attendance System

A digital attendance dashboard for Section B (Plant Protection) students at an agricultural university. Teachers take daily attendance per subject and period, view records, and download/print attendance sheets as CSV or PDF.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 8080)
- `pnpm --filter @workspace/attendance-dashboard run dev` — run the frontend (port 25183)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite + Wouter + TanStack Query + Tailwind CSS
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `lib/api-spec/openapi.yaml` — single source of truth for API contracts
- `lib/db/src/schema/` — Drizzle table definitions (students, subjects, attendance)
- `artifacts/api-server/src/routes/` — Express route handlers
- `artifacts/attendance-dashboard/src/` — React frontend

## Architecture decisions

- Attendance upsert: saving a session replaces existing records for the same student/subject/date/period combo
- All students default to "absent" in the take-attendance UI — teacher explicitly marks present
- The attendance sheet endpoint computes the full matrix server-side for easy CSV export on the client
- Orval zod config uses `mode: "single"` to avoid duplicate type exports between api.ts and generated/types/

## Product

- 146 Section B students pre-loaded with registration numbers, names, and CNICs
- 8 subjects with teacher names pre-loaded
- Take attendance per subject + period + date
- View past sessions in the Records page
- Download CSV or print attendance sheets from the Attendance Sheet page

## User preferences

- Section: B, Class: Plant Protection (2K26-PT-101 through 2K26-PT-247)
- Registration numbers skip 2K26-PT-246

## Gotchas

- `lib/api-zod/src/index.ts` must only export from `./generated/api` — do NOT add `./generated/types` export or it causes TS2308 duplicate export errors
- Always re-run codegen after changing `openapi.yaml`
