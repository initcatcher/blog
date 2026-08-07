# blog

I pick one paper, take one claim out of it, rebuild that claim in the smallest
amount of code that could settle it, and publish what happened. Failed
reproductions get published too.

Site: https://initcatcher.github.io/blog/

## Stack

Astro + [Fuwari](https://github.com/saicaca/fuwari), deployed to GitHub Pages
by `.github/workflows/deploy.yml`.

The theme was copied in, not forked, so the commit history here is mine.
Pinned at `saicaca/fuwari@6d39b0dec41282e7852e23e032998a5789abee28`
(2025-12-11). Upstream `main` has had no feature commits since; use that SHA as
the diff base if you ever want to pull changes in.

## Local

Requires Node 22 and pnpm 10.9.0 (pinned via `packageManager`).

```bash
pnpm install
pnpm dev      # localhost:4321
pnpm build    # astro build + pagefind index
pnpm preview
```

`pnpm install` needs `pnpm.onlyBuiltDependencies` in `package.json`. pnpm 10
blocks dependency lifecycle scripts by default, and both `sharp` and `pagefind`
fetch native binaries in postinstall. Without the allowlist, install succeeds
and the build fails later. Verify with:

```bash
node -e "require('sharp')" && pnpm exec pagefind --version
```

## Writing

```bash
pnpm new-post my-post-slug
```

Posts live in `src/content/posts/<slug>/index.md` with assets alongside.
Frontmatter is validated by the Zod schema in `src/content/config.ts`, so a
typo fails the build. `draft: true` hides a post in production only.

## Deploy

Every push to `main` builds and deploys. The workflow then runs a smoke check
against the live URL for four failures that would otherwise be silent: theme
defaults left in `src/config.ts`, a wrong `base` (HTML serves, every asset
404s), the post itself 404ing, and a missing search index.

Repo Settings → Pages → Source must be **GitHub Actions**.

## Roadmap

Phase 1 is this site being live. Phase 2 adds the X publishing pipeline,
`papers/`, and `experiments/`. Plan lives outside this repo.
