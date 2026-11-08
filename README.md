# pnpm cheat sheet

[INTENT: CONTEXT]

This file is the **table of contents only**. All substantive content lives under
[`docs/`](docs/) as capability areas. Do not add procedures, commands, or
troubleshooting text here — add or extend the matching area under `docs/` and
link it below.

## Structure

Content is organized by **subject anchor** (what area owns the truth), never by
document kind or operating system. OS is a facet inside a page, not a folder.

| Area | Owns | Entry |
|---|---|---|
| Installation | How `pnpm` itself gets onto a machine: sanctioned method, version pinning, native-binary verification, the Node-fallback anti-pattern | [`docs/installation/index.md`](docs/installation/index.md) |
| Update | Updating the `pnpm` CLI on a host, channel rule, verification | [`docs/update/index.md`](docs/update/index.md) |
| CLI | Everyday command surface (dependency operations, flags) | [`docs/cli/dependencies.md`](docs/cli/dependencies.md) |
| Builds | Native/build-script behavior of dependencies (e.g. `node-gyp` not building) | [`docs/builds/node-gyp-not-building.md`](docs/builds/node-gyp-not-building.md) |
| Workspace policy | `pnpm-workspace.yaml` behavior/policy surface (exact identity preserved as folder name) | [`pnpm-workspace.yaml/index.md`](pnpm-workspace.yaml/index.md) |
| Install hooks | `.pnpmfile.cjs` hooks and their governance (exact identity preserved as folder name) | [`.pnpmfile.cjs/index.md`](.pnpmfile.cjs/index.md) |

## Reading order for a new agent

1. `docs/installation/index.md` — the two governing laws (native-binary invariant, OS-as-facet) and the sanctioned install path.
2. `docs/installation/native-runtime-model.md` — how to verify an install is actually native.
3. `docs/update/index.md` — keep the tool current through its own channel.
4. `pnpm-workspace.yaml/index.md` — the hardened workspace control plane.

## Conventions this repo follows

- Folder names: kebab-case, subject-named; explicit source-side identities (`pnpm-workspace.yaml`, `.pnpmfile.cjs`) are preserved verbatim as folders with an `index.md`.
- Artifact type (`convention`, `troubleshooting`, `reference`) lives in the page and its heading, never in a folder name.
- Topic-first, facet-second: `docs/<capability>/<facet>/<leaf>.md`.
