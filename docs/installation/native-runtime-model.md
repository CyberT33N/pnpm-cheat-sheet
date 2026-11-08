# Native Runtime Model & Verification

[INTENT: SPECIFICATION]

What a correct pnpm 12 install looks like on disk, how to prove the native binary
is actually in use, and the supported-target / compatibility facts.

## The runtime model

pnpm 12 ships as an npm package that is a thin **wrapper**. Its real entry is a
native, per-platform binary delivered through an optional dependency
(`@pnpm/exe.<platform>-<arch>`). At install time `install.js` links that binary
over the placeholder bin files (`pnpm`, `pn`, `pnpx`, `pnx`), so every entry point
runs the compiled executable with no Node.js startup cost.

On disk (POSIX, `PNPM_HOME` layout):

```text
$PNPM_HOME/
├── bin/pnpm              # POSIX-sh shim; resolves $0, then execs the binary below
└── global/v11/<hash>/node_modules/pnpm/
    ├── pnpm              # the native ELF executable (healthy)  ~tens of MB
    ├── install.js        # links the binary over the placeholders
    └── native-binary.mjs # resolves @pnpm/exe.<platform>-<arch>
```

Two things must both be true for a healthy install:

1. the optional platform package `@pnpm/exe.<platform>-<arch>` was installed
   (it is an optional dependency, so `--no-optional` or a skipped install of
   optionals breaks it), and
2. `install.js` actually ran (a lifecycle-script block leaves the placeholder).

If either fails, the `pnpm` file is a small shell placeholder that runs
`node bin/pnpm.mjs` — functional, but not the intended runtime.

## Verify (the only reliable check)

`pnpm --version` succeeding proves nothing about the runtime. The PATH-visible
entry is only a shim — follow it to the binary it execs and check that:

```sh
file "$PNPM_HOME"/global/v11/*/node_modules/pnpm/pnpm
```

- Healthy on the standalone path: the shim's exec target is an
  `ELF 64-bit LSB ... executable` (or `Mach-O` on macOS, PE on Windows).
- Broken (Node fallback): the target is a `POSIX shell script` / shebang-less
  placeholder of a few KB, and stderr on a TTY prints
  `pnpm is running through Node.js ...` (see `troubleshooting/node-fallback-warning.md`).

The shim itself is always a small `sh` script — that is by design (a shebang-less
bin cannot be exec'd by the kernel). Judge the **resolved target**, not the shim.

## Supported targets (pnpm 12 ships a prebuilt binary for)

| OS | Architectures |
|---|---|
| Linux (glibc) | x64, arm64, riscv64, ppc64le, s390x |
| Linux (musl) | x64, arm64 |
| macOS | arm64, x64 |
| Windows | x64, arm64 |
| FreeBSD | x64 |
| Android | arm64, x64 |

On a target with no binary, install the JavaScript pnpm 11 instead.
(FreeBSD, ppc64le, s390x, RISC-V, Android were added in v12.4.0.)

## Node.js compatibility (only matters for the npm install path)

| Node.js | pnpm 10 | pnpm 11 | pnpm 12 |
|---|---|---|---|
| 18 | ✅ | ❌ | ✅ |
| 20 | ✅ | ❌ | ✅ |
| 22 | ✅ | ✅ | ✅ |
| 24 | ✅ | ✅ | ✅ |
| 26 | ✅ | ✅ | ✅ |

pnpm 12 only needs Node.js when installed **from npm**; the version installed by
the standalone script runs without Node.js. The `npx get-pnpm` installer requires
Node.js 22.13 or newer.

## Uninstall

See the official `pnpm.io/uninstall` — remove the `pnpm` CLI and the lines
`pnpm setup` wrote into the shell profile.
