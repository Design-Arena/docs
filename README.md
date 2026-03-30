# Design Arena API Documentation

This directory contains the source files for the public API documentation hosted on [Mintlify](https://docs.designarena.ai).

## How it works

- **Source of truth**: This directory (`designarena-docs/`) in the monorepo.
- **Public repo**: [Design-Arena/docs](https://github.com/Design-Arena/docs) — a publish target only. Nobody edits docs there directly.
- **Hosting**: Mintlify reads from the public repo and deploys automatically on push.

## Publishing docs

Run from the repo root:

```bash
./scripts/push-docs.sh "docs: describe your change"
```

This copies the current files to a temp directory, runs a secret scan, and pushes a single clean commit to the public repo. No monorepo history is replayed.

## DO NOT use `git subtree push`

**Never run `git subtree push` to publish docs.** It replays the monorepo's full commit history — including commits from repos that were merged in (like the old standalone console repo). This has already caused a secret leak (see `compliance/remediated-vulnerabilities-2026-Q1.md`).

The `push-docs.sh` script exists specifically to avoid this. Use it.

## Why not submodules?

We evaluated submodules and rejected them for this use case:

1. **Docs are edited in the monorepo**, alongside the code they document. Submodules would require a separate commit-and-push workflow in a nested repo — unnecessary friction.
2. **The public repo is a deploy target, not a dependency.** Submodules are for pulling external code *in*, not pushing internal content *out*.
3. **Submodules add clone complexity.** Everyone would need `--recurse-submodules`, and forgetting it results in an empty directory.

## File structure

```
designarena-docs/
├── mint.json              # Mintlify configuration
├── openapi.yaml           # OpenAPI spec for auto-generated endpoints
├── introduction.mdx       # Landing page
├── quickstart.mdx         # Getting started guide
├── api-reference/
│   ├── overview.mdx       # API overview + authentication
│   ├── arenas.mdx         # GET /arenas
│   ├── models.mdx         # GET /models
│   └── leaderboard.mdx    # GET /leaderboard
└── logo/                  # Brand assets
```
