# pnpm Installation

[INTENT: CONTEXT]

Capability area for getting the `pnpm` CLI onto a machine and keeping its runtime
correct. Sibling of `docs/update/`. This page is the map; the concrete procedures
live in the child pages below.

## Scope

This area owns the **tool bootstrap** (how `pnpm` itself is installed, pinned, and
verified on a host). It does not own project dependency operations (`add`, `update`,
`install` of a workspace's packages) or workspace policy — those are their own
areas (`docs/pnpm-workspace.yaml/`, `docs/.pnpmfile.cjs/`, `docs/update/`).

## The two governing laws

### 1. Native-binary invariant (🔐)

pnpm 12 is a **native executable**. After installation it must not require Node.js
to run. The install-time lifecycle script (`install.js` inside the `pnpm` npm
package) links a platform binary over the placeholder bins; if that script is
skipped, pnpm silently keeps a Node-interpreted fallback. A correct install is
therefore verified by the **file type of the resolved binary**, not by
`pnpm --version` succeeding (see `native-runtime-model.md`).

### 2. OS is a facet, not a level

The operating system changes only *how* pnpm is bootstrapped (one shell branch per
platform); it never changes *what* pnpm is. OS variants therefore live as
sections/leaves **inside** a method, never as a top-level `os-agnostic/` or
`windows/` folder. A folder that groups by document-kind or platform instead of by
subject owns no owner, no freshness cycle, and no retrieval intent — it is a
classification container and is forbidden here. This matches the existing
`docs/update/troubleshooting/nvm.md` precedent (topic-first, facet-second).

## Method decision

| Situation | Use | Page |
|---|---|---|
| Fresh machine / no pnpm yet | Standalone script (no Node required afterwards) | `methods.md` → Standalone |
| Reproducible CI / pinned `packageManager` | Standalone script with `PNPM_VERSION` | `methods.md` → Version pinning |
| pnpm already installed, move forward | `pnpm self-update` | `methods.md` → self-update |
| npm-installed pnpm inherited | Migrate to the standalone channel | `methods.md` → npm (anti-pattern) |
| Something is wrong / warning appears | diagnose, then fix | `troubleshooting/node-fallback-warning.md` |

**Sanctioned default: the standalone script only.** npm-global is documented as an
anti-pattern (❌) for recognition and migration, never as an install choice — it
is vendor-supported but reaches the target architecture in no scenario (runtime
placement depends on lifecycle-script policy, the binary is coupled to the Node
prefix, and the CLI's runtime stays ambient).

## Pages

- `methods.md` — the install paths ranked against the target architecture, with
  platform variants and the official version-pinning mechanism.
- `native-runtime-model.md` — what a correct install looks like on disk, how to
  verify the native binary, and the Node/pnpm compatibility matrix.
- `troubleshooting/node-fallback-warning.md` — the "running through Node.js"
  warning: root cause, why it matters, and the fix.
