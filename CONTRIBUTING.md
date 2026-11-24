# Contributing to llm-meter

## Development setup

```bash
git clone https://github.com/stackgauge/llm-meter.git
cd llm-meter
npm install
npm run build
npm test
npm run lint
```

## Branching & pull requests

- `main` is protected — **no direct pushes**. All changes land via pull request.
- Branch from `main` with a short prefix: `feat/…`, `fix/…`, `chore/…`, `docs/…`.
- Open a PR. CI (lint + build + tests on Node 18/20/22) must pass before merge.

## Conventional Commits (required)

Commit messages — and PR titles, since we squash-merge — follow
[Conventional Commits](https://www.conventionalcommits.org/), which drives
automated versioning and the changelog:

| Type | When | Release effect |
| ---- | ---- | -------------- |
| `feat:` | New user-facing feature | Minor bump |
| `fix:` / `perf:` | Bug fix / perf improvement | Patch bump |
| `docs:` `test:` `refactor:` `chore:` `ci:` | Everything else | No release |

A breaking change uses `!` (e.g. `feat!:`) or a `BREAKING CHANGE:` footer.

## How releases work

Merging Conventional-Commit PRs into `main` makes
[release-please](https://github.com/googleapis/release-please) open a Release PR
that bumps the version and updates `CHANGELOG.md`. When a maintainer merges that
Release PR, a tag + GitHub Release are created and the release workflow publishes
to npm via **OIDC Trusted Publishing** (no tokens). You never run `npm publish`
locally.
