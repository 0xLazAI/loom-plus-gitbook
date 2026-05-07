# Publishing This GitBook

This page is for maintainers publishing the Loom+ user guide through GitBook Git Sync.

## Repository

Intended source repository:

[0xLazAI/loom-plus-gitbook](https://github.com/0xLazAI/loom-plus-gitbook)

The GitBook source in this app workspace currently lives in:

```text
docs/gitbook/
```

The repository root contains:

```yaml
root: ./docs/gitbook/

structure:
  readme: README.md
  summary: SUMMARY.md
```

GitBook uses this config to find the documentation root and table of contents.

## Publish With Git Sync

1. Create or open the GitBook space for Loom+.
2. Enable Git Sync.
3. Connect the `0xLazAI/loom-plus-gitbook` repository.
4. If using this monorepo directly, keep `.gitbook.yaml` at the repo root.
5. If using a dedicated docs repo, copy the files from `docs/gitbook/` to that repo root and either remove `.gitbook.yaml` or set `root: ./`.
6. Publish the GitBook space.
7. Set `NEXT_PUBLIC_GITBOOK_URL` in the Loom+ deployment to the published GitBook URL.

## App Links

The Loom+ app reads GitBook links from:

```text
src/lib/gitbook.ts
```

Default values:

- GitBook URL: `https://0xlazai.gitbook.io/loom-plus`
- Source repo: `https://github.com/0xLazAI/loom-plus-gitbook`
- loomcli repo: `https://github.com/0xLazAI/loomcli`

## Current Local Limitation

This environment is not logged in to GitHub CLI, so it cannot create the remote `0xLazAI/loom-plus-gitbook` repository automatically.

After logging in with an account that can create repositories in `0xLazAI`, run:

```bash
gh repo create 0xLazAI/loom-plus-gitbook --private --source docs/gitbook --remote origin --push
```

If the repository should be public, replace `--private` with `--public`.
