# pnpm Update

[INTENT: CONTEXT]

Capability area for updating the pnpm CLI itself on a machine. Updating project
dependencies (`pnpm up`) is owned by `docs/cli/`; the initial install and the
native-binary model are owned by `docs/installation/`.

## Channel rule (🔐)

Update pnpm **through the channel that installed it**. Mixing channels leaves
multiple pnpm binaries on PATH and produces version shadowing — the failure mode
documented in `troubleshooting/nvm.md`.

| Installed via | Update via |
|---|---|
| Standalone script (`get.pnpm.io`) | `pnpm self-update` |
| npm global (anti-pattern default, see `docs/installation/methods.md`) | `npm install -g pnpm@<version>` |

## Commands

Update the standalone install to the latest release line:

```sh
pnpm self-update
```

Update to a specific line or exact version:

```sh
pnpm self-update 12
pnpm self-update 12.17.0
```

Inside a project that pins pnpm through the `packageManager` field,
`self-update` updates that pin instead of moving the global install.

## Verification

After any update:

```sh
pnpm --version
command -v pnpm
```

Confirm the resolved path belongs to the intended channel (`$PNPM_HOME/bin` for
the standalone install), then re-check the native binary per
`docs/installation/native-runtime-model.md` — an update that silently re-created
the Node.js fallback is a regression, not a success.

## Anti-patterns

- ❌ Installing a second pnpm through another channel "to get a newer version"
  while the first is still on PATH — that is shadowing, not an update.
- ❌ Letting the global pnpm version drift from the project's `packageManager`
  pin and "fixing" it by weakening `pmOnFail`/engine strictness instead of
  updating the tool.

Related: `docs/installation/` (install, verification, npm anti-pattern),
`troubleshooting/nvm.md` (PATH repair on Windows).
