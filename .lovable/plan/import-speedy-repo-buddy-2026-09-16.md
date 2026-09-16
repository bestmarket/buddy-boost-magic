# Import speedy-repo-buddy

The repository is a complete video-studio app: a landing page, sign-in, and a signed-in area with dashboard, sources, studio, channels, chat and admin pages, plus a scheduled-jobs endpoint. It was built on the same stack this project uses, so the import is a faithful copy plus backend setup.

## What gets rebuilt here

1. **All app code and pages** — landing page, sign-in page, and the signed-in pages: dashboard, sources, studio, channels, chat, admin. Same look and behaviour as the source.
2. **Backend (Lovable Cloud)** — turned on for this project, then the full database is recreated: projects, videos, source videos, sources, ideas, scripts, posts, channels, profiles, user roles, AI settings, AI providers, usage tracking and schedule config, with the same access rules.
3. **Storage** — the private `media` bucket the video features write to.
4. **Sign-in** — email sign-in plus Google sign-in, matching the source app.
5. **Scheduled videos endpoint** — kept at the same public path, with its shared-secret check.
6. **AI features** — routed through the built-in Lovable AI, as in the source.

## What does not carry over automatically

- **Existing accounts and data.** A new backend starts empty; the old project's users and rows are not transferable through the repository. If you want the existing content here, export it from the old project and send the files, and I will load it in.
- **Service keys.** Anything the app needs for YouTube publishing or outside AI providers must be added as secrets; I will ask for those securely when they are needed. The scheduled-jobs secret gets generated here.

## Technical notes

- Copy `src/` (routes, components, lib, hooks, integrations), `public/`, config files and dependencies from the checkout; keep the target template's pinned TanStack versions rather than the source lockfile.
- Enable Lovable Cloud, then apply the four SQL migrations in order (`0000`, `0002`, `0003`, plus the no-op files), including grants, RLS policies and the `has_role` security-definer function; re-point `supabase/config.toml` at the new project.
- Regenerate `src/integrations/supabase/*` against the new backend and keep `auth-middleware`, `client.server`, `cron-auth` and the auth attacher in `src/start.ts`.
- Secrets to configure: `LOVABLE_CRON_SECRET` (generated), plus provider keys on request. `LOVABLE_API_KEY` comes from Cloud.
- Verify: build, then sign in with a temporary account, load each route, create and delete one project row, and remove the test account.
