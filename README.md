# AIStartupSEO.com — The skills.md File That Built a SaaS

[![Skills.md for AI Coding Assistants](https://aistartupseo.com/api/uploads/blog/1789936857061-2ed35096b73f7b6a.png)](https://aistartupseo.com/blog/vibe-coding/the-exact-skills-md-file-i-used-to-build-a-successful-saas-using-claude-code)

> **The exact `skills.md` instruction file used to build [AIStartupSEO.com](https://aistartupseo.com) — a production SaaS with 1M+ queued reports, 5 OAuth providers, Stripe payments, AI-powered pipelines, and 35+ admin sections. Built by one developer using Claude Code.**

---

## What Is This?

`skills.md` is a persistent instruction file that sits in your project root and tells AI coding assistants (Claude Code, Cursor, Copilot, Windsurf, Codex, Gemini CLI) **how to work in your codebase** — before they touch a single file.

AI coding tools are stateless. Every new session starts from zero context. Without persistent instructions, you waste half your time re-explaining your project conventions, tech stack, file structure, and coding patterns.

This file solves that. Drop it in your repo, and every AI session starts with full context about:

- Your tech stack and defaults
- File and folder conventions
- API route patterns and response shapes
- Database workflow (Prisma, migrations, indexing)
- Security non-negotiables
- UI/UX and landing page principles
- Git commit conventions
- A self-improvement loop that makes the AI learn from its mistakes

**This is not a template or boilerplate. It's the actual file used in production** to build [AIStartupSEO.com](https://aistartupseo.com) from first commit to a live product serving thousands of users.

---

## Why skills.md Exists

Building a full SaaS product with AI coding assistants is absolutely possible — but only if you solve the context problem.

Without `skills.md`, here's what happens:

- The AI forgets your file structure and creates files in the wrong places
- It "helps" by rewriting 200 lines of working code when you asked it to change one thing
- It switches between yarn and npm randomly
- It adds abstractions you didn't ask for
- It introduces security vulnerabilities (no input validation, missing auth checks)
- It uses `any` types everywhere
- Every session starts with 10 minutes of re-explaining your project

With `skills.md`, the AI reads your rules first and follows them consistently. The most important rule in the entire file is **Section 3.2: "Don't Break What Works"** — it exists because I lost an entire afternoon to an AI "helpfully" refactoring my auth system while I asked it to fix a button color.

---

## Full Story

Read the complete breakdown of the workflow, process, and lessons learned:

**[The Exact skills.md File I Used to Build a Successful SaaS Using Claude Code](https://aistartupseo.com/blog/vibe-coding/the-exact-skills-md-file-i-used-to-build-a-successful-saas-using-claude-code)**

The article covers:
- The Google Antigravity IDE + Claude Code workflow
- How the three-file system (`todo.md`, `dev.md`, `deploy.md`) prevents context loss
- Why persistent AI instructions compound over time
- Tips for adapting skills.md to your own stack
- What AIStartupSEO.com looks like after being built entirely with this process

---

## Quick Start

### Option 1: Copy the file directly

1. Download [`skills.md`](./skills.md) from this repo
2. Place it in your project root
3. Edit **Section 1 (Default Tech Stack)** to match your stack
4. Start your AI coding session — it will read the file automatically

### Option 2: Clone this repo

```bash
git clone https://github.com/jveritas/aistartupseo-skills-md.git
cp skills-md/skills.md /path/to/your/project/
```

### Option 3: Use with Claude Code

Claude Code automatically reads markdown files in your project root. Just drop `skills.md` into your repo:

```bash
curl -O https://raw.githubusercontent.com/jveritas/aistartupseo-skills-md/main/skills.md
```

Then start Claude Code — it picks up the instructions immediately.

### Option 4: Use with Cursor / Copilot / Other AI IDEs

Most AI coding tools support project-level instruction files:

| Tool | Where to place instructions |
|------|---------------------------|
| **Claude Code** | `skills.md` or `CLAUDE.md` in project root |
| **Cursor** | `.cursor/rules/` directory or `.cursorrules` file |
| **GitHub Copilot** | `.github/copilot-instructions.md` |
| **Windsurf** | `.windsurfrules` file |
| **Codex / ChatGPT** | Reference the file in your prompt |

Copy the relevant sections from `skills.md` into whichever format your tool uses.

---

## What's Inside skills.md

| Section | What It Covers |
|---------|---------------|
| **1. Default Tech Stack** | Next.js, TypeScript, Tailwind, PostgreSQL, Prisma, NextAuth, Stripe, SendGrid |
| **2. Core Project File Management** | The three-file system: `todo.md`, `dev.md`, `deploy.md` |
| **3. Workflow Orchestration** | Plan before building, the Golden Rule, self-improvement loop, verification |
| **4. Code Standards & Patterns** | File conventions, API route structure, database workflow, error handling |
| **5. Security Essentials** | Input validation, auth checks, XSS/CSRF prevention, Stripe webhook verification |
| **6. UI/UX Principles** | Landing page structure, Tailwind conventions, dark mode, mobile-first, copywriting |
| **7. Git Conventions** | Commit message format, gitignore, branch workflow |
| **8. Performance & Optimization** | Images, fonts, bundle size, database queries, client components |
| **9. Third-Party Integrations** | Stripe, SendGrid, NextAuth patterns |
| **10. Data Processing** | CSV/XLSX parsing, bulk operations, progress tracking |
| **11. Pre-Delivery Checklist** | Verification steps before marking any task complete |
| **12. Agent Skills Ecosystem** | Community skills from skills.sh, obra/superpowers, Claude Code templates |

---

## The Three-File System

The most universally useful pattern in this repo — even if you ignore everything else:

### `todo.md` — Your Project Plan
Ordered tasks grouped into phases. Markdown checkboxes for progress tracking. Never start a new phase until the current one is 100% complete. The AI updates this automatically as it works.

### `dev.md` — Developer Guide & Living Documentation
The source of truth for your project's current state. Tech stack versions, architecture overview, API inventory, session logs, reverted decisions, known issues, and a **Lessons** section that compounds over time. Every mistake the AI makes gets documented here so it never happens again.

### `deploy.md` — Deployment From Zero
Step-by-step instructions so anyone (or any AI agent) can deploy from a fresh machine. Localhost setup with a unique port, environment variables, database setup, production deployment, and verification checklist.

**Why this matters:** AI sessions are ephemeral. These three files are persistent. They bridge the gap between sessions and give every new AI interaction full project context without you having to explain anything.

---

## Adapting to Your Stack

This file is opinionated toward the stack used to build AIStartupSEO.com. Here's how to adapt it:

| If you use... | Change this section... |
|--------------|----------------------|
| **Supabase** instead of raw Prisma | Section 4.3 — update database workflow |
| **tRPC** instead of REST API routes | Section 4.2 — update API patterns |
| **Svelte / Vue** instead of React | Section 4.1 — update file conventions |
| **Drizzle** instead of Prisma | Section 4.3 — update ORM patterns |
| **Better Auth** instead of NextAuth | Section 9 — update auth patterns |
| **Resend** instead of SendGrid | Section 9 — update email patterns |
| **Vercel** instead of self-hosted | Section 2C — simplify deploy.md |

The structure and workflow rules (Sections 2, 3, 5, 7) are stack-agnostic and work regardless of what you're building with.

---

## What Was Built With This

[**AIStartupSEO.com**](https://aistartupseo.com) — a platform that helps AI startups get discovered through SEO reports, directory submissions, and automated email outreach.

Built with this exact `skills.md` file using Claude Code in Google Antigravity IDE:

- **Next.js 16** App Router with TypeScript
- **PostgreSQL** via Prisma (~2,300 line schema)
- **NextAuth v4** with Google, GitHub, LinkedIn, Twitter OAuth + credentials
- **Stripe** payments with 3 plan tiers
- **AI pipeline** using Gemini, OpenAI, Anthropic, and AWS Bedrock
- **Email campaigns** via AWS SES with warmup progression + SendGrid for transactional
- **1M+ report queue** with anti-starvation processing and priority re-ranking
- **35+ admin sections** for managing every aspect of the platform
- **Full scraper pipeline**: ProductHunt, TheresAnAI, domain validation, B2B enrichment

One developer. AI-assisted. Shipped to production.

---

## Contributing

Found a pattern that makes AI coding better? Open a PR.

- Add patterns that are **stack-agnostic** and **battle-tested** (you've used them in a real project)
- Keep additions concise — this file works because AI assistants actually read the whole thing
- Don't add framework-specific setup guides — those belong in your project's own `dev.md`

---

## Star This Repo

If `skills.md` saves you time, **star this repo** so others can find it. The more people use and improve this pattern, the better AI-assisted development gets for everyone.

---

## License

MIT — use it however you want. Attribution appreciated but not required.

---

## Links

- [Full article: The Exact skills.md File I Used to Build a Successful SaaS Using Claude Code](https://aistartupseo.com/blog/vibe-coding/the-exact-skills-md-file-i-used-to-build-a-successful-saas-using-claude-code)
- [AIStartupSEO.com](https://aistartupseo.com) — the product built with this process
- [skills.sh](https://skills.sh) — community skills directory for AI coding agents
- [obra/superpowers](https://github.com/obra/superpowers) — structured AI development workflow
- [Claude Code](https://claude.ai/code) — the AI coding assistant used
- [Google Antigravity IDE](https://antigravity.google/product/antigravity-ide/) — the cloud IDE used
