# replit.md

## Overview

This is a full-stack web application using a React frontend and Express backend with PostgreSQL for data storage. The project follows a monorepo structure with shared code between client and server. It's currently a starter/scaffold project with basic user management (users table) and no application-specific pages or routes implemented yet.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Directory Structure
- **`client/`** — React frontend (SPA)
- **`server/`** — Express backend (API server)
- **`shared/`** — Shared code between client and server (database schema, types)
- **`migrations/`** — Drizzle ORM database migrations
- **`script/`** — Build scripts

### Frontend Architecture
- **Framework:** React with TypeScript
- **Bundler:** Vite (dev server with HMR, production build)
- **Routing:** Wouter (lightweight client-side router)
- **State Management:** TanStack React Query for server state
- **UI Components:** shadcn/ui (new-york style) built on Radix UI primitives
- **Styling:** Tailwind CSS with CSS variables for theming, class-variance-authority for component variants
- **Forms:** React Hook Form with Zod resolvers
- **Path aliases:** `@/` maps to `client/src/`, `@shared/` maps to `shared/`

### Backend Architecture
- **Framework:** Express 5 on Node.js
- **Language:** TypeScript, run with `tsx`
- **API pattern:** All API routes should be prefixed with `/api`
- **Dev server:** Vite middleware is integrated into Express during development for HMR
- **Production:** Static files served from `dist/public`, server bundled to `dist/index.cjs` via esbuild
- **Storage layer:** Abstracted via `IStorage` interface in `server/storage.ts`. Currently uses in-memory `MemStorage` implementation. Can be swapped to a database-backed implementation.

### Database
- **ORM:** Drizzle ORM with PostgreSQL dialect
- **Schema location:** `shared/schema.ts`
- **Schema push:** `npm run db:push` (uses drizzle-kit)
- **Connection:** Requires `DATABASE_URL` environment variable
- **Validation:** Drizzle-zod for generating Zod schemas from database tables
- **Current schema:** Single `users` table with `id` (UUID, auto-generated), `username` (unique text), `password` (text)

### Session Management
- `connect-pg-simple` is listed as a dependency for PostgreSQL-backed session storage (not yet wired up)
- `express-session` is available for session handling

### Build Process
- **Dev:** `npm run dev` — runs Express + Vite dev server with HMR
- **Build:** `npm run build` — builds client with Vite, bundles server with esbuild
- **Start:** `npm start` — runs production build from `dist/index.cjs`
- **Type check:** `npm run check`
- Server build bundles common dependencies (listed in allowlist in `script/build.ts`) to reduce cold start times

### Key Patterns
- The frontend `apiRequest` helper in `client/src/lib/queryClient.ts` handles API calls with credentials included and JSON content type
- React Query is configured with infinite stale time and no automatic refetching — data is manually invalidated
- 401 handling is configurable per query (return null or throw)
- The `cn()` utility merges Tailwind classes using `clsx` + `tailwind-merge`

## External Dependencies

### Database
- **PostgreSQL** — Required. Connection string via `DATABASE_URL` environment variable
- **Drizzle ORM** — Schema management and query building
- **drizzle-kit** — Database migration and schema push tooling

### Key NPM Packages
- **express** (v5) — HTTP server
- **react** + **react-dom** — UI framework
- **@tanstack/react-query** — Server state management
- **wouter** — Client-side routing
- **zod** — Runtime validation
- **drizzle-zod** — Bridge between Drizzle schema and Zod validation
- **shadcn/ui** components (Radix UI primitives) — Full component library installed
- **recharts** — Charting library (via chart.tsx component)
- **vaul** — Drawer component
- **embla-carousel-react** — Carousel component
- **lucide-react** — Icon library
- **date-fns** — Date utilities
- **react-day-picker** — Calendar/date picker

### Optional/Available (in dependencies but not fully wired)
- **passport** + **passport-local** — Authentication
- **express-session** + **connect-pg-simple** — Session management with PostgreSQL store
- **stripe** — Payment processing
- **openai** + **@google/generative-ai** — AI integrations
- **nodemailer** — Email sending
- **multer** — File upload handling
- **ws** — WebSocket support
- **jsonwebtoken** — JWT handling
- **xlsx** — Spreadsheet processing

### Replit-Specific
- **@replit/vite-plugin-runtime-error-modal** — Runtime error overlay in dev
- **@replit/vite-plugin-cartographer** — Dev tooling (dev only)
- **@replit/vite-plugin-dev-banner** — Dev banner (dev only)