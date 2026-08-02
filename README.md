# Grant

> Grant is a private context platform for people and teams. Create one living profile, connect it to your AI agents through MCP and OAuth, and keep your work style, goals, preferences, and team context consistent across every agent.

**One file across every agent.**

Grant keeps a curated context profile in plain Markdown. Connected agents can read the sections you permit, propose updates, and direct-edit only where you have allowed it. Personal and company workspaces use the same model, with company roles, invitations, section permissions, activity, archive and version history.

## Quickstart

Prerequisites: **Node 20+**, a free **Supabase** project, and an **OpenRouter key** for AI features.

```bash
git clone https://github.com/devndesigner6/grant.git
cd grant
npm ci
cp .env.example .env.local
supabase link --project-ref <your-project-ref> && supabase db push
npm run dev
```

Minimum `.env.local`:

```bash
NEXT_PUBLIC_SITE_URL=http://localhost:3000
NEXT_PUBLIC_SUPABASE_URL=https://<project>.supabase.co
NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY=<publishable-key>
SUPABASE_SECRET_KEY=<service-role-key>
GRANT_ENCRYPTION_SECRET=$(openssl rand -base64 32)
GRANT_CSRF_SECRET=$(openssl rand -base64 32)
GRANT_HEALTH_SECRET=$(openssl rand -base64 32)
```

Grant is free for authenticated users. AI features use the encrypted OpenRouter key that a user or company owner saves in Settings.

## Connect an agent

Open `/connections` and add the Grant MCP URL to your agent as a custom connector. Grant is an OAuth 2.1 authorization server, so spec-compliant MCP clients can connect from the server URL and authorize in the browser.

Grant supports Claude Code, Codex, Cursor, ChatGPT, OpenCode, and custom MCP clients. Connected agents can read permitted context and propose or make permitted edits. Grant CLI source remains in `packages/creed-cli/` while that legacy folder name is retained for repository compatibility. Install the published CLI with `npm install --global @devndesigner/grant-cli`, then run `grant`.

## Stack

| Layer | Choice |
|---|---|
| Framework | Next.js 16, React 19, TypeScript |
| UI | Tailwind CSS v4, shadcn/ui, Tiptap, Framer Motion |
| Backend | Supabase Auth, Postgres, RLS, realtime, audit logs |
| AI | OpenRouter BYOK with encrypted key storage |
| Sync | GitHub Markdown push/pull |

## Repository map

```text
app/                    Next.js routes, OAuth, MCP, and authenticated APIs
components/             Product, auth, marketing, and UI components
lib/                    Supabase, AI, MCP, security, and GitHub integration
supabase/migrations/    Immutable forward-only schema history
packages/creed-cli/     Compatibility-sensitive Grant CLI source
tests/                  node:test suites
```

## Commands

```bash
npm run dev
npm test
npx tsc --noEmit -p .
npm run lint
npm run build
```

## Deployment setup

Create a Supabase project, configure the required values from `.env.example`, then apply the immutable migration history:

```powershell
npx supabase login
npx supabase link --project-ref <project-ref>
npx supabase db push
```

Use `http://localhost:3000` locally. After Vercel provides the real deployment URL, set `NEXT_PUBLIC_SITE_URL` to it and update Supabase Site URL and Redirect URLs. Configure GitHub OAuth with `<NEXT_PUBLIC_SITE_URL>/auth/github/callback`, then set `GITHUB_OAUTH_CLIENT_ID` and `GITHUB_OAUTH_CLIENT_SECRET`. Grant is one Vercel Next.js deployment, using root `./`, `npm ci`, `npm run build`, and Node 20+. Its MCP endpoint is `<NEXT_PUBLIC_SITE_URL>/mcp`.

OpenRouter is BYOK-only. Users and company owners save encrypted keys in Settings; `ANALYSIS_MODEL`, `TAB_MODEL`, and `PANEL_MODEL` select models when set. Without a key, Grant shows “Add your OpenRouter key in Settings.” Invitations always create a real acceptance URL. Add `RESEND_API_KEY` and `RESEND_FROM_EMAIL` later for branded email delivery; otherwise copy the real invite link. Verify detailed health with `X-Grant-Health-Secret`.

The published CLI package is `@devndesigner/grant-cli`. Build and test the source in `packages/creed-cli` before publishing a new version with `npm publish --access public`.

## Contributing

Read [`CONTRIBUTING.md`](./CONTRIBUTING.md). AI coding agents should read [`AGENTS.md`](./AGENTS.md).

Report security issues through [`SECURITY.md`](./SECURITY.md), not a public issue.

## License

[MIT](./LICENSE)
