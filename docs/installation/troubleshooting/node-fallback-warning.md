# "pnpm is running through Node.js"

[INTENT: SPECIFICATION]

## Symptom

On every `pnpm` invocation, stderr prints (only when stderr is a TTY, so CI and
agent runs stay silent):

```text
pnpm is running through Node.js because the script that installs its native binary was skipped.
Reinstall pnpm with its build scripts allowed to run the binary directly.
```

`pnpm --version` still works. The install is functional but degraded.

## Root cause

The `pnpm` npm package is a wrapper whose real entry is a native binary from the
optional dependency `@pnpm/exe.<platform>-<arch>`, linked over the placeholder
bins by `install.js`. The warning means that link step did not happen. Two
independent triggers:

1. **Lifecycle script skipped** — the install path blocked build scripts
   (`--ignore-scripts`, a package manager that runs no lifecycle scripts, or a
   policy that skipped pnpm's own install script). The placeholder stays and
   hands off to `node bin/pnpm.mjs`.
2. **Optional dependency not installed** — `@pnpm/exe.*` was filtered out
   (`--no-optional`, a store without it). `install.js` then has no binary to
   link and exits with `The "<pkg>" package is not installed, so pnpm has no
   native binary to run.`

This is a **machine-level** condition. It is not caused by, and must not be
fixed in, a project's `pnpm-workspace.yaml`: `allowBuilds`/`onlyBuiltDependencies`
govern a workspace's dependencies, and `pnpm` is not a project dependency. Editing
workspace policy for this is a category error (machine state leaking into the
governance control plane).

## Why it matters (not just cosmetic)

- Extra process hop and Node startup cost on **every** pnpm call.
- **Reproducibility coupling:** the Node fallback lets the caller's `PATH`
  decide *which* `node` runs pnpm; the native binary is self-contained. Your
  package-manager authority should not depend on the ambient Node version.
- On a fresh install / CI cache miss the placeholder path may also lazily fetch
  the binary at first run (an extra network hop).

## Fix — reinstall on the standalone path, pinned

Reinstall pnpm itself through the official standalone script (which needs no
lifecycle script to place the native binary), pinned to the version your projects
declare so the `packageManager`/`.tool-versions` pins stay congruent:

```sh
curl -fsSL https://get.pnpm.io/install.sh | env PNPM_VERSION=<version> sh -
source ~/.zshrc   # or the profile pnpm setup edited
```

Do not "fix" it by globally disabling script-safety (e.g. blanket
`--ignore-scripts=false` on a shared machine or `dangerouslyAllowAllBuilds`): the
standalone path sidesteps the whole lifecycle-script dependency and is the
authoritative route.

## Verify the fix

```sh
# The shim itself is sh by design; judge the binary it execs:
file "$PNPM_HOME"/global/v11/*/node_modules/pnpm/pnpm
```

Expected: `ELF 64-bit LSB ... executable` (tens of MB), not a few-KB shell
placeholder. Then confirm the workspace still resolves:

```sh
pnpm --version
pnpm install --frozen-lockfile
```

## Related

- Upstream report of the allowBuilds flip-flop that leaves pnpm on the Node path:
  pnpm/pnpm#14926.
- `native-runtime-model.md` — the healthy on-disk layout and the full
  verification method.
