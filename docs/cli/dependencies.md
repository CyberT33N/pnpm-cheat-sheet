# CLI — Dependency Commands

[INTENT: SPECIFICATION]

Leaf of the `cli` capability area for everyday dependency operations.
Full CLI reference: https://pnpm.io/pnpm-cli

## Update one dependency to its latest version

```sh
pnpm up eslint --latest
```

`--latest` moves the declared range to the newest published major line,
bypassing the currently declared range. Without it, `pnpm up` only updates
within the existing ranges.

## Catalog-governed workspaces

In a workspace run with `catalogMode: strict`, dependency versions are approved
in the catalog (`pnpm-workspace.yaml`) — the single version-approval surface.
Update the catalog entry and re-install; do not move a leaf's version with a
per-leaf `pnpm up` and thereby take the dependency out of catalog governance.
