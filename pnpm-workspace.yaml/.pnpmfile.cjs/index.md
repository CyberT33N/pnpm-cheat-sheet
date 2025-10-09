## .pnpmfile.cjs (install hooks)
Use sparingly, but it’s the right place to centralize security rails and shared defaults.

- 🔐 ✅ `hooks.updateConfig(config)` 
  - Enforce defaults from a single place (like a “better defaults” plugin).
  - Example:
    ```js
    // .pnpmfile.cjs
    module.exports = {
      hooks: {
        updateConfig (config) {
          return {
            ...config,
            engineStrict: true,
            strictPeerDependencies: true,
            autoInstallPeers: false,
            resolutionMode: 'time-based',
            enablePrePostScripts: false,
            strictDepBuilds: true,
            verifyDepsBeforeRun: 'error'
          }
        }
      }
    }
    ```

- 🔐 ⚠️ `hooks.readPackage(pkg)` 
  - Patch manifests in-flight (e.g., pin/replace/remove risky deps). Prefer `overrides`/`packageExtensions` first, use hook if those are insufficient.
  - Example:
    ```js
    function readPackage(pkg, ctx) {
      if (pkg.name === 'left-pad' && pkg.version.startsWith('1.')) {
        pkg.dependencies = { ...pkg.dependencies, 'left-pad': '^1.2.3' }
        ctx.log('Pinned left-pad to 1.2.3')
      }
      return pkg
    }
    module.exports = { hooks: { readPackage } }
    ```

- ✅ `hooks.afterAllResolved(lockfile)` 
  - Final lockfile mutation if you must enforce extra metadata.
  - Example:
    ```js
    module.exports = {
      hooks: {
        afterAllResolved(lockfile) {
          // e.g., assert all tarball URLs use HTTPS
          return lockfile
        }
      }
    }
    ```

Security note: prefer `overrides`, `packageExtensions`, and workspace policy first; hooks are powerful but increase maintenance/audit scope.
