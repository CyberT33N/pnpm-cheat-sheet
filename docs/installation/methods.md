# Installation Methods

[INTENT: SPECIFICATION]

The install paths, ranked against the target architecture, with platform variants
inline (OS is a facet — see `index.md`). Source: official pnpm installation
documentation.

**Convention verdict:** the standalone script is the **only sanctioned default**.
npm-global is vendor-supported but never the highest target architecture and is
documented below as an anti-pattern (❌) so an inherited install can be recognized
and migrated — recognition is not endorsement.

## Standalone script (preferred)

Installs pnpm without a pre-existing Node.js and leaves a native binary behind.
The script runs `pnpm setup` for you, which sets `PNPM_HOME` and adds it to the
shell profile.

### On POSIX systems

```sh
curl -fsSL https://get.pnpm.io/install.sh | sh -
```

Without `curl`:

```sh
wget -qO- https://get.pnpm.io/install.sh | sh -
```

### On Windows

PowerShell:

```powershell
Invoke-WebRequest https://get.pnpm.io/install.ps1 -UseBasicParsing | Invoke-Expression
```

> Windows Defender may block the executable on this path. The correct answer is
> the exclusion, not a channel switch — after a successful install add the store
> to the exclusion list (admin PowerShell), which also removes slow installs:
> `Add-MpPreference -ExclusionPath (pnpm store path)`.
> The official docs name npm as the fallback for this case; under this repo's
> convention that is an exception requiring a recorded reason, not the default.

### In a Docker container

```sh
# bash
wget -qO- https://get.pnpm.io/install.sh | env ENV="$HOME/.bashrc" SHELL="$(which bash)" bash -
# sh
wget -qO- https://get.pnpm.io/install.sh | env ENV="$HOME/.shrc"  SHELL="$(which sh)"  sh -
# dash
wget -qO- https://get.pnpm.io/install.sh | env ENV="$HOME/.dashrc" SHELL="$(which dash)" dash -
```

### Version pinning

Set `PNPM_VERSION` before piping so a specific version is installed instead of
`latest`. Use it to keep the machine congruent with a project's `packageManager`
field:

```sh
curl -fsSL https://get.pnpm.io/install.sh | env PNPM_VERSION=<version> sh -
```

## Using npm — anti-pattern (❌)

Do not install the pnpm CLI this way. It is vendor-supported, but it never
reaches the target architecture, in every scenario:

1. **Runtime placement depends on lifecycle-script policy.** The native binary is
   linked by `install.js`; any npm policy that skips lifecycle scripts leaves the
   Node-interpreted placeholder silently in place
   (`troubleshooting/node-fallback-warning.md`).
2. **Placement is coupled to the Node prefix.** The global binary lands inside the
   active Node installation, so every Node version manager (nvm, mise, proto,
   asdf) shadows or loses it — the exact failure documented in
   `docs/update/troubleshooting/nvm.md`.
3. **The package manager's own runtime is ambient.** pnpm runs through whichever
   `node` the caller's `PATH` resolves, instead of being self-contained.

`npx get-pnpm` shares the same weakness: it needs Node.js to run and installs
through the same npm wrapper.

```sh
# ❌ not sanctioned as a default
npm install -g pnpm@latest
npx get-pnpm
```

**Inherited an npm-installed pnpm?** Migrate to the sanctioned channel instead of
maintaining it — install via the standalone script (pinned to the version your
projects declare), then verify per `native-runtime-model.md`:

```sh
curl -fsSL https://get.pnpm.io/install.sh | env PNPM_VERSION=<version> sh -
```

## Using pnpm (self-update)

If pnpm 11.10.0 or newer is already present, update directly to pnpm 12:

```sh
pnpm self-update
```

Inside a project that pins pnpm through the `packageManager` field, `self-update`
updates that pin rather than installing a different version globally.

## After install

```sh
source ~/.zshrc   # or the profile pnpm setup edited for your shell
pnpm --version
```
