# node-gyp Is Not Built Despite `gypfile: true`

[INTENT: SPECIFICATION]

## Symptom

`pnpm install` does not run `node-gyp` for a dependency that has no `postinstall`
script of its own. Setting `"gypfile": true` in that package's `package.json`
should start the gyp build, but does not.

## Cause

With `strictDepBuilds` (the enterprise default) install scripts run only for
packages explicitly approved in the build allow-list (`onlyBuiltDependencies` /
`allowBuilds`). A newly added approval is **not retroactively applied to an
already-installed tree**: adding the package to the allow-list and re-running
`pnpm install` or `pnpm rebuild` still does not trigger the gyp step for a
package that was first installed while unapproved.

## Resolution

Clear the installed tree so the approval takes effect from scratch:

```powershell
Set-Location "C:\Projects\test"
Remove-Item ".\node_modules" -Recurse -Force

# Usually sufficient without deleting the lockfile:
# Remove-Item ".\pnpm-lock.yaml" -Force -ErrorAction SilentlyContinue

pnpm install
```

The lockfile deletion is the escalation path, not the default — try the
`node_modules` removal first.
