# Antigravity IDE & LLM System Instructions (skills.md)

**CRITICAL INSTRUCTION FOR ALL LLMs:** Read this entire document before executing any commands. This file dictates your workflow, coding standards, file management, architecture, and UI/UX approach for this project.

---

## 1. Default Tech Stack

Unless the project explicitly specifies otherwise, use this stack for all new projects:

| Layer | Technology | Notes |
|---|---|---|
| Framework | Next.js 14+ (App Router) | Always use `/app` directory, NOT `/pages` |
| Language | TypeScript (strict mode) | Never use `any` type — use `unknown` + type guards |
| Styling | Tailwind CSS 3+ | No custom CSS files unless absolutely necessary |
| Database | PostgreSQL | Via managed service (Neon, Supabase) or self-hosted |
| ORM | Prisma | Always the source of truth for DB schema |
| Auth | NextAuth.js v5 (Auth.js) | CredentialsProvider for email/password projects |
| Payments | Stripe (Checkout Sessions) | Always use hosted checkout, never embedded card forms |
| Email | SendGrid (Dynamic Templates) | For transactional. AWS SES for bulk/marketing later |
| Hosting | AWS Lightsail or Hetzner + Plesk | Or Vercel for frontend-only deploys |
| Package Manager | npm | Not yarn, not pnpm, unless project already uses one |

**Font defaults:** Geist Sans (headings) + Geist Mono (code/numbers), or Inter as fallback. Never use Arial, Roboto, or Times New Roman.

**Color approach:** One bold accent color + neutral grays. Define in Tailwind config AND CSS variables for consistency.

---

## 2. Core Project File Management

Maintain and continuously update these three markdown files in the project root. If they do not exist, create them immediately based on project context.

### A. `todo.md` (Project Plan)
- **Purpose:** Ordered tasks and sequential execution.
- **Format:** Group tasks into logical "Phases" (e.g., Phase 0: Setup, Phase 1: Auth).
- **Actionable:** Use markdown checkboxes `[ ]` for checkable items so progress is visually tracked.
- **Rule:** Never start a new phase until the current one is 100% complete and verified.
- **After completing a task:** Mark it `[x]` immediately. If a task spawns sub-tasks, add them inline before moving on.

### B. `dev.md` (Developer Guide & Status)
- **Purpose:** The source of truth for the project's current state.
- **Header section — always include:**
  - Tech stack with exact versions (`next@14.2.3`, `prisma@5.x`, etc.)
  - Hosting environment and URLs (localhost, staging, production)
  - Brand variables: primary color hex, font names, border-radius convention
  - Test accounts: email/password for admin and member roles
  - Environment variables list (names only, never values)
- **Architecture section:**
  - Summarized file tree of key routes and components (not every file)
  - Database schema summary (model names + key fields)
  - API route inventory with HTTP methods
- **Session Logs section ("Latest Session Work"):**
  - What was done, when, why, and how
  - **Reverted decisions** — document what was tried and why it was rolled back, so neither you nor a future LLM repeats the mistake
  - **Known issues** — bugs or incomplete features that still need attention
- **Lessons section:**
  - Patterns and anti-patterns discovered during development
  - This grows over time and is the project's institutional knowledge

### C. `deploy.md` (Deployment Guide)
- **Purpose:** Step-by-step instructions so anyone (or any AI agent) can deploy from zero.
- **Localhost section MUST come first**, with these specifics:
  - **Assign a unique port** for this project (NOT the default `3000`). Pick a random 4-digit port between `3100–3999` at project init. Write it in `deploy.md` AND configure it in `package.json`:
    ```json
    "scripts": {
      "dev": "next dev -p 3247"
    }
    ```
  - This prevents port conflicts when running multiple projects simultaneously, and lets you bookmark `localhost:3247` for easy access.
  - Include the exact commands: `npm install` → `npm run dev` → verify at `http://localhost:XXXX`
  - List any required services (PostgreSQL running, Stripe CLI listening, etc.)
- **Must also include:**
  - Prerequisites (Node version, npm version, system dependencies)
  - Environment variable setup (where to get each key, where to put them)
  - Database setup (create DB, run migrations, seed data)
  - Production deployment steps (build, upload, configure reverse proxy, SSL)
  - Stripe webhook endpoint configuration
  - DNS and domain setup
  - Post-deploy verification checklist

---

## 3. Workflow Orchestration

### 3.1 Plan Before You Build
- Enter plan mode for ANY non-trivial task (3+ steps or architectural decisions).
- If something goes sideways, **STOP and re-plan immediately** — don't keep pushing forward hoping it'll work out.
- Write the plan as comments or pseudocode before writing real code.
- For database schema changes: always plan the migration path BEFORE touching `schema.prisma`.

### 3.2 The Golden Rule: Don't Break What Works
- **Before modifying any existing file**, read and understand its current behavior.
- **Never refactor working code** unless explicitly asked to. If you need to add a feature, add it alongside existing code — don't restructure.
- **If a file is longer than 200 lines**, only modify the specific section needed. Don't rewrite the entire file.
- **After every change**, verify the existing functionality still works before moving on.
- If you accidentally break something: revert the change first, THEN figure out the right approach.

### 3.3 Self-Improvement Loop
- After ANY correction from the user: update `dev.md` Lessons section with the pattern.
- Write rules for yourself that prevent the same mistake twice.
- Review the Lessons section at the start of each session.
- Common mistake patterns to watch for:
  - Importing from wrong paths after moving files
  - Forgetting to update both client AND server when changing an API shape
  - Breaking Tailwind responsive design by adding desktop-only styles
  - Overwriting user customizations when regenerating components

### 3.4 Verification Before Done
- Never mark a task complete without proving it works.
- For UI changes: describe what the page looks like now and confirm it matches intent.
- For API changes: show a test request/response.
- For database changes: verify with a query that data is correct.
- Ask yourself: "If I refresh the browser right now, does everything still work?"

### 3.5 Demand Elegance (But Ship First)
- For the first implementation: **make it work**. Ugly code that works beats elegant code that doesn't.
- After it works: **then** clean it up if the code is genuinely hard to understand or maintain.
- Skip refactoring for simple, obvious fixes — don't over-engineer.
- Never add an abstraction layer "for future use" — add it when you actually need it.

### 3.6 Autonomous Bug Fixing
- When given a bug report: just fix it. Don't ask for hand-holding or clarification unless the bug description is genuinely ambiguous.
- Read error messages carefully — they usually tell you exactly what's wrong.
- Check the browser console, terminal output, and server logs before guessing.
- After fixing: explain what was wrong and why, in plain language.

---

## 4. Code Standards & Patterns

### 4.1 File & Folder Conventions
```
src/
├── app/                    # Next.js App Router pages and API routes
│   ├── (public)/           # Publicly accessible pages (landing, login, pricing)
│   ├── (dashboard)/        # Auth-required member pages
│   ├── (admin)/            # Admin-only pages
│   └── api/                # API routes
├── components/
│   ├── ui/                 # Reusable primitives (Button, Input, Modal, Card)
│   └── [feature]/          # Feature-specific components (dashboard/, landing/, admin/)
├── lib/                    # Shared utilities, configs, helpers
│   ├── prisma.ts           # Prisma client singleton
│   ├── auth.ts             # Auth configuration
│   ├── stripe.ts           # Stripe helpers
│   └── utils.ts            # General utilities
├── types/                  # Shared TypeScript types/interfaces
└── hooks/                  # Custom React hooks
```

- **File naming:** `kebab-case.ts` for utilities, `PascalCase.tsx` for React components.
- **One component per file.** If a component grows past 150 lines, split it.
- **Colocate related files.** A page's loading state, error boundary, and layout go in the same folder.

### 4.2 API Route Patterns
Every API route should follow this structure:

```typescript
// Always validate input with Zod
// Always check auth via getServerSession()
// Always return consistent response shapes
// Always wrap in try/catch

export async function POST(req: Request) {
  try {
    // 1. Auth check
    const session = await getServerSession(authOptions);
    if (!session) return Response.json({ error: "Unauthorized" }, { status: 401 });

    // 2. Parse & validate input
    const body = await req.json();
    const parsed = mySchema.safeParse(body);
    if (!parsed.success) return Response.json({ error: parsed.error.flatten() }, { status: 400 });

    // 3. Business logic
    const result = await prisma.thing.create({ data: parsed.data });

    // 4. Return success
    return Response.json({ data: result }, { status: 201 });
  } catch (error) {
    console.error("[API_ROUTE_NAME]", error);
    return Response.json({ error: "Internal server error" }, { status: 500 });
  }
}
```

**Response shape — always consistent:**
- Success: `{ data: ... }`
- Error: `{ error: "message" }` or `{ error: { field: ["message"] } }`

### 4.3 Database & Prisma Workflow
- `schema.prisma` is the **single source of truth** for database structure.
- **To change the database:**
  1. Edit `schema.prisma`
  2. Run `npx prisma db push` (development) or `npx prisma migrate dev` (when using migrations)
  3. Run `npx prisma generate` to update the TypeScript client
  4. Update any affected API routes and types
- **Always use a Prisma singleton** (`lib/prisma.ts`) — never instantiate `new PrismaClient()` in route files.
- **Index frequently queried fields** with `@@index([fieldName])` in the schema.
- **Use `cuid()` for IDs** — not auto-increment integers, not UUIDs (cuids are shorter and URL-safe).

### 4.4 Error Handling
- **Server-side:** Every `async` function that touches the database or external API must be wrapped in `try/catch`.
- **Client-side:** Use React Error Boundaries for component-level crashes. Use `try/catch` in event handlers.
- **API calls from the frontend:** Always handle loading, success, and error states. Never assume a fetch will succeed.
- **User-facing errors:** Show a helpful message ("Something went wrong. Please try again."), never raw error dumps.
- **Developer errors:** Log full error details to `console.error` with a tag: `[STRIPE_WEBHOOK]`, `[AUTH]`, etc.

### 4.5 Environment Variables & Secrets
- **NEVER hardcode** API keys, database URLs, secrets, or passwords in code.
- All secrets go in `.env` (local) and environment variables on the server.
- Maintain a `.env.example` file with all variable names (no values) and comments explaining each.
- **Client-side env vars** in Next.js MUST be prefixed with `NEXT_PUBLIC_`. If a var doesn't need to be in the browser, don't prefix it.
- **Never log environment variables** — not even partial values.

```env
# .env.example
DATABASE_URL=             # PostgreSQL connection string
NEXTAUTH_SECRET=          # Random 32+ char string (generate: openssl rand -base64 32)
NEXTAUTH_URL=             # http://localhost:3000 for dev, https://yourdomain.com for prod
STRIPE_SECRET_KEY=        # From Stripe Dashboard → Developers → API Keys
STRIPE_WEBHOOK_SECRET=    # From Stripe Dashboard → Webhooks → Signing Secret
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=  # From Stripe Dashboard (starts with pk_)
SENDGRID_API_KEY=         # From SendGrid → Settings → API Keys
```

---

## 5. Security Essentials

These are non-negotiable. Apply to every project.

- **Input validation:** Validate ALL user input server-side with Zod. Never trust client-side validation alone.
- **Auth checks:** Every API route that modifies data or returns private data must verify the session. Every admin route must verify `role === "ADMIN"`.
- **SQL injection:** Not a concern with Prisma (it parameterizes queries), but NEVER build raw SQL strings from user input if using raw queries.
- **XSS prevention:** React escapes by default, but never use `dangerouslySetInnerHTML` with user-provided content.
- **CSRF:** NextAuth handles CSRF tokens for auth routes. For custom forms, use the `csrf` package or validate the `Origin` header.
- **Rate limiting:** Add rate limiting to auth routes (login, register, password reset) and any expensive API routes. Use `@upstash/ratelimit` or a simple in-memory counter for dev.
- **Stripe webhooks:** ALWAYS verify the webhook signature before processing. Never trust unverified webhook payloads.
- **Password storage:** ALWAYS hash with bcrypt (minimum 10 rounds). Never store plaintext passwords. Never log passwords.
- **File uploads:** If accepting uploads, validate file type and size server-side. Never execute uploaded files.

---

## 6. UI/UX & "Vibe Coding" Principles

When generating UI components or landing pages, follow these principles optimized for fast shipping, high conversion, and a modern indie SaaS aesthetic.

### A. Landing Page Structure (High Conversion)
- **Above the Fold:** Clear, punchy H1 (problem → solution). Descriptive subheadline. Primary CTA button. Social proof immediately visible.
- **Section flow:** Problem → Solution → What's Included → How It Works → Social Proof → Pricing → FAQ → Final CTA.
- **Pricing:** Brutal clarity. Anchor the value ("$87 lifetime vs. $500/mo agency"). One recommended plan visually highlighted.
- **Frictionless:** Minimize nav links that distract from the CTA. Every section should funnel toward checkout.
- **Objection Handling:** Always include a clean FAQ section. Address the top 5–7 objections buyers will have.
- **Scarcity/Urgency:** Use real scarcity (limited early pricing, increasing price as product grows), never fake countdown timers or "only 3 left" when it's not true.

### B. Visual Aesthetics & Tailwind
- **High contrast, clean layouts.** One bold accent color for CTAs and interactive elements. Neutral grays for everything else.
- **Typography hierarchy:** Display font (heavy weight) for H1/H2. Body font (regular weight) for paragraphs. Monospace for numbers, stats, and code. Minimum 3 distinct levels of visual hierarchy on every page.
- **Spacing is king:** Generous padding. Sections breathe. Cards have consistent internal spacing. Use Tailwind's spacing scale consistently (`p-6`, `gap-4`, `mb-8` — not random pixel values).
- **Micro-interactions:** Subtle hover states on buttons and cards (`hover:scale-[1.02]`, `transition-all duration-200`). Focus states for accessibility. Loading spinners on async actions.
- **Dark mode:** Support natively via `next-themes` or Tailwind's `dark:` prefix. Design dark-first if the brand leans techy/developer.
- **Border radius convention:** Pick one and stick to it project-wide. Either sharp (`rounded-lg` everywhere) or pill (`rounded-full` for buttons, `rounded-2xl` for cards). Never mix randomly.
- **Avoid AI slop:** No purple-to-blue gradients on white backgrounds. No generic "futuristic" aesthetics. No stock photo vibes. Make it look like a real human designed it for a specific audience.

### C. Copywriting Tone
- **Punchy and direct.** Write like a human talking to another human. No corporate jargon.
- **Action-oriented buttons:** Use verbs. "Get Lifetime Access", "Start Submitting", "View Sample Data" — never "Submit", "Click Here", "Learn More".
- **Specific numbers sell:** "Submit to 458+ directories" is better than "Submit to hundreds of directories".
- **No filler paragraphs.** Every sentence should either inform or persuade. If a paragraph can be cut without losing meaning, cut it.

### D. User Dashboard Experience
- **Optimistic UI:** Update the UI instantly on user action, sync to DB in background. Show a subtle checkmark/toast on sync success. Show error toast + revert on sync failure.
- **Empty states:** Never show a blank page. Every empty table/list shows a designed empty state with an illustration or icon, explanation text, and a CTA ("Start by adding your first project details →").
- **Gamification:** Progress bars per category. Overall completion percentage. "X of Y submitted" counters. These keep users engaged and give them a sense of momentum.
- **Tables on mobile:** Convert data tables to card layouts on screens below `md` breakpoint. Never show a horizontally scrolling table on mobile.
- **Loading states:** Skeleton loaders (gray animated blocks in the shape of content) while data loads. Never show a blank page or just a spinner.
- **Toast notifications:** Use for save confirmations, errors, and important state changes. Auto-dismiss after 3–5 seconds. Position: bottom-right.

---

## 7. Git & Version Control Guide

**Context: The user is learning Git. Handle version control proactively.**

### Basics the LLM should enforce:
- **Commit after every completed task** (not after every file change — after a logical unit of work).
- **Commit messages:** Use present tense, imperative mood. Format: `type: short description`
  - `feat: add stripe checkout flow`
  - `fix: resolve login redirect loop`
  - `style: update dashboard card spacing`
  - `chore: add sendgrid env vars to .env.example`
- **Never commit `.env` files.** Ensure `.gitignore` includes: `.env`, `.env.local`, `node_modules/`, `.next/`, `prisma/*.db`.
- **Before starting work:** `git pull` to get latest changes (if collaborating or using multiple machines).
- **If something breaks badly:** `git stash` to save current changes, `git checkout .` to revert all files to last commit, then re-approach the problem.

### `.gitignore` template (include in every project):
```
node_modules/
.next/
.env
.env.local
.env.production
*.db
prisma/migrations/
.vercel/
```

---

## 8. Performance & Optimization

Apply these by default — don't wait for performance problems.

- **Images:** Always use Next.js `<Image>` component (auto-optimizes, lazy loads, serves WebP). Never use raw `<img>` tags. Store images in `/public` or use external URLs.
- **Fonts:** Use `next/font` to self-host Google Fonts. This eliminates render-blocking external requests.
- **Bundle size:** Don't install large libraries for small tasks. Use native browser APIs when possible (e.g., `fetch` instead of `axios`, `Intl.NumberFormat` instead of a formatting library).
- **Database queries:** Select only the fields you need (`select: { id: true, name: true }` in Prisma). Use pagination for large datasets. Add `@@index` for fields used in `where` clauses.
- **Client components:** Keep `"use client"` components as small and as low in the tree as possible. The more Server Components you have, the less JavaScript ships to the browser.
- **Dynamic imports:** Use `next/dynamic` for heavy components that aren't needed on initial page load (charts, editors, modals).

---

## 9. Third-Party Integration Patterns

### Stripe
- Always use **Stripe Checkout (hosted)** for payments — never build custom card forms.
- Create products and prices in the **Stripe Dashboard**, then reference price IDs in code.
- Use `metadata` on Checkout Sessions to pass context (plan type, user ID) to the webhook.
- **Webhook handler:** Verify signature → extract event → process based on `event.type` → return 200. Always return 200 quickly, even if processing fails (handle errors asynchronously or log them).
- Test with Stripe CLI: `stripe listen --forward-to localhost:3000/api/stripe/webhook`

### SendGrid
- Use **Dynamic Templates** (not hardcoded HTML) — design emails in the SendGrid template editor.
- Pass template variables as `dynamic_template_data` in the API call.
- Set up **Sender Authentication** (domain verification) before going to production, or emails will land in spam.
- For dev/testing: use a verified single sender email.

### NextAuth
- Always use the **Prisma Adapter** for database-backed sessions.
- Extend the session type to include `role` and `userId` (add to `callbacks.session`).
- Protect routes with **middleware** (`src/middleware.ts`), not individual page checks.
- For password auth: hash with `bcrypt`, compare with `bcrypt.compare`.

---

## 10. Data Processing & Large Datasets

For projects involving CSV/XLSX parsing, data migration, or bulk operations:

- **Use streaming** for files over 10MB — don't load entire files into memory.
- **XLSX parsing:** Use the `xlsx` (SheetJS) package. Read with `{ type: 'buffer' }` and iterate rows with `XLSX.utils.sheet_to_json()`.
- **CSV parsing:** Use `papaparse` for browser-side, `csv-parse` (from `csv` package) for server-side.
- **Database bulk inserts:** Use `prisma.model.createMany()` with `skipDuplicates: true`. Batch in chunks of 500–1000 rows.
- **Progress tracking:** For long-running imports, emit progress via Server-Sent Events or write progress to a status record in the DB that the frontend polls.
- **Data cleanup:** Strip whitespace, normalize URLs (remove trailing slashes), parse numeric strings to actual numbers, handle "N/A" and empty strings as `null`.

---

## 11. Checklist: Before Asking User to Test

Run through this mentally before saying "try it now":

- [ ] Does `npm run dev` start without errors?
- [ ] Does the page load in the browser without console errors?
- [ ] Do all links/buttons navigate correctly?
- [ ] Do forms validate and show errors properly?
- [ ] Does the feature work when logged in AND when logged out (as appropriate)?
- [ ] Does it look correct on mobile (check at 375px width)?
- [ ] Are all environment variables documented in `.env.example`?
- [ ] Is `dev.md` updated with what was just built?
- [ ] Is the task checked off in `todo.md`?

---

## 12. Agent Skills Ecosystem — Don't Reinvent the Wheel

Before building any non-trivial feature, check if a community skill or template already exists. The AI coding ecosystem now has a package-manager-like layer for agent skills — use it.

### 12.1 skills.sh — The Skills Directory

**https://skills.sh/** is the central registry for reusable AI agent skills. Think of it like npm, but for LLM coding agent capabilities. Skills are installed with one command and enhance what the agent can do.

**When to check skills.sh:**
- Before building any common feature (auth, payments, testing, deployment, UI design)
- When the user says "help me with X" and X is a well-known domain
- When starting a new project and deciding on patterns

**How to search:**
```bash
npx skills find [query]        # Search by keyword
npx skills add <owner/repo>   # Install a skill
npx skills check               # Check for updates
```

**Browse the leaderboard at https://skills.sh/ — prioritize skills with:**
- 10K+ installs (battle-tested)
- Official sources: `vercel-labs`, `anthropics`, `microsoft`, `expo`, `supabase`, `shadcn`
- Avoid skills under 100 installs from unknown authors

**High-value skills relevant to our stack (as of 2025):**

| Skill | Source | What It Does |
|---|---|---|
| `find-skills` | vercel-labs/skills | Meta-skill: helps discover other skills |
| `vercel-react-best-practices` | vercel-labs/agent-skills | React + Next.js patterns from Vercel engineering |
| `web-design-guidelines` | vercel-labs/agent-skills | Web design best practices |
| `next-best-practices` | vercel-labs/next-skills | Next.js App Router patterns |
| `ui-ux-pro-max` | nextlevelbuilder/ui-ux-pro-max-skill | Professional UI/UX design intelligence |
| `frontend-design` | anthropics/skills | Anthropic's own frontend design skill |
| `systematic-debugging` | obra/superpowers | Structured debugging methodology |
| `verification-before-completion` | obra/superpowers | Ensures work is verified before marking done |
| `supabase-postgres-best-practices` | supabase/agent-skills | PostgreSQL patterns (if using Supabase) |
| `neon-postgres` | neondatabase/agent-skills | Neon DB patterns (if using Neon) |
| `better-auth-best-practices` | better-auth/skills | Auth implementation patterns |
| `seo-audit` | coreyhaines31/marketingskills | SEO analysis and optimization |
| `copywriting` | coreyhaines31/marketingskills | Landing page and marketing copy |
| `programmatic-seo` | coreyhaines31/marketingskills | Programmatic SEO strategies |
| `launch-strategy` | coreyhaines31/marketingskills | Product launch planning |

### 12.2 obra/superpowers — Agentic Development Methodology

**https://github.com/obra/superpowers** (40K+ stars, MIT license) is a complete software development workflow framework for coding agents. It's not just a collection of skills — it's an opinionated *methodology* that changes how the LLM approaches your work. Compatible with Claude Code, Codex, OpenCode, Gemini CLI, and others.

**What it does differently:** Instead of the agent jumping straight into writing code, Superpowers forces a structured workflow:
1. **Brainstorming** — the agent asks clarifying questions and refines your idea into a spec
2. **Planning** — it creates an implementation plan clear enough for a junior engineer to follow
3. **Subagent-driven execution** — it launches sub-agents per task, reviews their work, and continues autonomously (often for hours without drifting)
4. **Verification** — built-in red/green TDD, code review requests, and completion checks

**Install in Claude Code:**
```bash
# Register the marketplace
/plugin marketplace add obra/superpowers-marketplace

# Install the plugin
/plugin install superpowers@superpowers-marketplace

# Verify — you should see /superpowers:brainstorm, /superpowers:write-plan, /superpowers:execute-plan
/help
```

**Install via skills.sh:**
```bash
npx skills add obra/superpowers
```

**Key skills included (also installable individually via skills.sh):**

| Skill | Installs | What It Does |
|---|---|---|
| `brainstorming` | 58K+ | Interactive design refinement before coding |
| `writing-plans` | 30K+ | Creates detailed, sequential implementation plans |
| `executing-plans` | 25K+ | Runs through plan tasks with subagents |
| `systematic-debugging` | 32K+ | Structured root-cause analysis methodology |
| `test-driven-development` | 27K+ | Red/green TDD enforcement |
| `verification-before-completion` | 19K+ | Prevents marking tasks done without proof |
| `subagent-driven-development` | 21K+ | Parallel agent orchestration for complex tasks |
| `requesting-code-review` | 25K+ | Self-review before presenting work |
| `dispatching-parallel-agents` | 18K+ | Multi-agent task distribution |

**When to use Superpowers vs. just skills.md rules:**
- Use Superpowers for **greenfield projects** or **complex multi-phase builds** where the structured brainstorm → plan → execute → verify cycle adds value.
- Use your `skills.md` alone for **quick fixes, small features, and maintenance work** where the overhead of formal planning isn't justified.
- They work well together — Superpowers handles the workflow orchestration while your `skills.md` handles project-specific conventions, stack choices, and coding standards.

### 12.3 Claude Code Templates (aitmpl.com)

**When using Claude Code specifically**, the `claude-code-templates` project (22.5K+ GitHub stars) provides pre-built agents, custom commands, MCPs, hooks, and settings configurations:

```bash
# Browse interactively
npx claude-code-templates@latest

# Install a specific agent
npx claude-code-templates@latest --agent development-team/frontend-developer --yes

# Install a specific MCP integration
npx claude-code-templates@latest --mcp database/postgresql-integration --yes
```

**Browse all templates at https://aitmpl.com**

Source: https://github.com/davila7/claude-code-templates

### 12.4 Other Useful Skill Repositories

Keep these bookmarked for reference:

| Repository | Stars | What It Offers |
|---|---|---|
| `alirezarezvani/claude-skills` | — | 36 professional role-based skills (PM, designer, architect, etc.) |
| `coreyhaines31/marketingskills` | — | Full marketing skill set: SEO, copywriting, pricing, launch strategy |
| `wshobson/agents` | — | 48 development agents: TypeScript, API design, architecture patterns |

### 12.5 When NOT to Use External Skills

- If the skill adds complexity that doesn't match the project scope
- If the skill is for a framework/language you're not using
- If the skill has fewer than 100 installs and no audit badges on skills.sh
- If your `skills.md` already covers the topic adequately — don't duplicate instructions
- Always prefer your project's own `skills.md` rules over generic external skills when they conflict
