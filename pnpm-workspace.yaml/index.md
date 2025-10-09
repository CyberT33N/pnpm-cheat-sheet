
## pnpm-workspace.yaml (behavior/policy) 🏢
The authoritative source for pnpm behavior. Below is a hardened, enterprise‑grade baseline with ES2024+ in mind.

```yaml
# Security posture
minimumReleaseAge: 1440            # 🔐 ✅ delay 24h to avoid fresh hijacks
engineStrict: true                 # 🔐 ✅ enforce engines during dependency install
strictPeerDependencies: true       # 🔐 ✅ fail on missing/invalid peers
strictDepBuilds: true              # 🔐 ✅ fail on unreviewed postinstall scripts
autoInstallPeers: false            # 🔐 ✅ require explicit peer decisions (esp. for libs)

# Node & package manager control
useNodeVersion: 22.7.0             # ✅ pin runtime used by pnpm run/exec
nodeVersion: 22.7.0                # ✅ check new deps against node engines
packageManagerStrict: true         # ✅ enforce pnpm usage
packageManagerStrictVersion: true  # 🔐 ✅ exact pnpm version match
managePackageManagerVersions: true # ✅ auto-manage pnpm version in packageManager

# Resolution & lockfile
preferFrozenLockfile: true         # 🔐 ✅ headless, deterministic installs
resolutionMode: time-based         # 🔐 ✅ safer subdep updates (less hijacks)
registrySupportsTimeField: false   # ⚠️ set true if Verdaccio ≥5.15.1
sharedWorkspaceLockfile: true      # 🏢 ✅ single lockfile across the monorepo

# Workspace linking
linkWorkspacePackages: true        # 🏢 ✅ local linking (symlinks) for speed
preferWorkspacePackages: true      # 🏢 ✅ prefer local over registry
saveWorkspaceProtocol: true        # 🏢 ✅ write workspace:* specs
savePrefix: ''                     # 🏢 ✅ pin exact within workspace

# Hoisting/Node linker
nodeLinker: isolated               # ✅ stable, strict node_modules with symlinks
hoist: true                        # ⚠️ allow controlled hoisting for flawed tools
hoistPattern:                      # ⚠️ only hoist known phantom-dep offenders
  - "*eslint*"
  - "*babel*"
publicHoistPattern: []             # 🔐 avoid exposing phantom deps to app code
shamefullyHoist: false             # 🔐 ❌ never blanket-hoist

# Modules & store
virtualStoreDir: .pnpm             # ✅ shorter paths, cleaner stack traces
virtualStoreDirMaxLength: 120      # ✅ keep windows-safe length
packageImportMethod: clone-or-copy # ✅ safe across filesystems (CoW when possible)
modulesCacheMaxAge: 10080          # 🧪 reuse orphan cache for faster branch switches
dlxCacheMaxAge: 1440               # 🧪 speed up pnpm dlx reuse
verifyStoreIntegrity: true         # 🔐 ✅ content check before linking
storeDir: ${XDG_DATA_HOME:-${HOME}/.local/share}/pnpm/store # ✅ predictable

# Build script security
onlyBuiltDependenciesFile: node_modules/.pnpm-config/@pnpm/trusted-deps/allow.json # 🔐 ✅ strict allow-list
neverBuiltDependencies:            # 🔐 block native/platform dependent builds unless needed
  - fsevents
ignoredBuiltDependencies:          # 🔐 skip building deps known to be prebuilt
  - esbuild
dangerouslyAllowAllBuilds: false   # 🔐 ❌ never blanket-allow all scripts
enablePrePostScripts: false        # 🔐 prefer explicit pnpm run pre/post if needed
verifyDepsBeforeRun: error         # 🔐 ✅ forbid running scripts on stale node_modules

# Peer dependency resolution
resolvePeersFromWorkspaceRoot: true # 🏢 ✅ single source of truth for peers
dedupePeerDependents: true          # ✅ dedupe when peers are compatible
peerDependencyRules:
  ignoreMissing: []                 # 🔐 avoid suppressing warnings unless vetted
  allowedVersions: {}               # 🔐 set narrowly if needed
  allowAny: []                      # 🔐 avoid blanket allow

# Overrides & extensions
overrides: {}                      # 🔐 pin/bisect transitive deps or remove with "-"
packageExtensions: {}              # 🔐 patch missing peers of third-party packages
allowedDeprecatedVersions: {}      # 🔐 whitelist intentionally if necessary

# Catalog governance
catalogMode: strict                # 🏢 🔐 version catalog is the source of truth
cleanupUnusedCatalogs: true        # 🏢 ✅ remove stale catalog entries

# CI ergonomics
ci: true                           # ✅ when under CI to optimize defaults
optimisticRepeatInstall: true      # ✅ fast no-op for repeat installs
requiredScripts:                   # 🏢 enforce available scripts across packages
  - build
shellEmulator: true                # ✅ cross-platform script execution
includeWorkspaceRoot: false        # 🏢 avoid running recursive scripts at root
disallowWorkspaceCycles: true      # 🏢 fail on cycles early

# Optional: branch-based lockfiles in high-conflict repos
gitBranchLockfile: false           # ⚠️ enable only with merge automation
mergeGitBranchLockfilesBranchPattern: null
```

Key workspace‑only items: 🏢 on `linkWorkspacePackages`, `preferWorkspacePackages`, `saveWorkspaceProtocol`, `sharedWorkspaceLockfile`, `catalogMode`, `cleanupUnusedCatalogs`, `includeWorkspaceRoot`, `disallowWorkspaceCycles`.

Monorepo vs published libs vs applications:
- 📦 Libraries:
  - Set `autoInstallPeers: false`, `strictPeerDependencies: true`, curate `peerDependencies`.
  - Use `publishConfig.directory` + `files` allow‑list.
  - Avoid hoisting reliance; keep public API esm/cjs/types aligned.
- 🚀 Applications:
  - Prefer `resolutionMode: time-based`, may selectively hoist with `hoistPattern`.
  - Ensure deploy paths via `pnpm deploy` or `pnpm fetch` + offline layer in CI.
- 🏢 Workspace:
  - `linkWorkspacePackages`, `preferWorkspacePackages`, `saveWorkspaceProtocol`, `catalogMode: strict`.

Examples for critical options already shown in the YAML block above (each option includes a working value).
