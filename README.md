# Design Arena API Documentation

This directory is a **git submodule** pointing to the public [Design-Arena/docs](https://github.com/Design-Arena/docs) repository. Mintlify reads from that repo and deploys automatically on push.

## Setup (first time after cloning the monorepo)

```bash
git submodule update --init designarena-docs
```

## Editing docs

Edit files directly in `designarena-docs/` — it's a normal directory. The only difference is that changes are committed to the submodule's own repo.

## Publishing docs

Run from the monorepo root:

```bash
./scripts/push-docs.sh "docs: describe your change"
```

This will:
1. Scan for secrets with gitleaks
2. Commit changes inside the submodule
3. Push to the public `Design-Arena/docs` repo
4. Stage the updated submodule pointer in the monorepo

After running the script, commit the monorepo to record the new submodule pointer:

```bash
git add designarena-docs && git commit -m "chore: update docs submodule pointer"
```

## Why a submodule (not a subtree)?

We previously used `git subtree push` which replays the monorepo's full commit history into the target repo. This caused a secret leak when historical commits from a merged repo (the old standalone console repo) were replayed into the public docs repo. See `compliance/remediated-vulnerabilities-2026-Q1.md` for details.

A submodule is a completely separate git repository — the monorepo only stores a commit pointer to it. There is no mechanism by which monorepo history can leak into the docs repo.

## DO NOT use `git subtree push`

**Never run `git subtree push` to publish docs.** Use the script above or commit directly inside the submodule.
