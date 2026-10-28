# Source provenance for the published CEF archives

This repository publishes **prebuilt** CEF binary archives under
[Releases](https://github.com/obs-rust/cef/releases). CEF is BSD-3-licensed, so
nothing here is required by licence — this file exists because a hash pin with
no named source is a pin you cannot repeat.

For every published archive it records the upstream CEF commit it was built
from, the recipe (repo + commit) that produced it, whether any patch from a
branch of *this* repository is applied, and the archive's sha256.

The same record exists in machine-readable form, one file per release, under
[`releases/`](releases). Each release tag points at the commit that adds its own
file, so the record for a release is readable from the tag itself:

```sh
git show 8078:releases/8078.json
```

A tag never resolves to a commit that is not that release's record. `7871` was
a mirror of an upstream archive and its manifest says so rather than naming a
source commit it cannot name.

## Summary

| Release | Assets | Built by | Upstream CEF source | Patch commit | Recipe (obs-rust/obs-studio-libs) |
|---|---|---|---|---|---|
| `7871` | linux x86_64/aarch64, macOS x86_64/arm64, windows x64/arm64 | upstream OBS (mirror) | CEF 7871 / Chromium 150 — upstream binary, commit not recorded | none applied | none (mirror) |
| `8078` | linux x86_64 | obs-rust (container) | `chromiumembedded/cef` @ `426920c33ef22551e0b8f48cf343d623ac25642d` (`156.0.8+g426920c+chromium-156.0.8078.48`) | none applied | `recipes/cef/linux` @ `e653bfb` |
| `8078` | macOS arm64, macOS x86_64 | @nathan-v (own Apple Silicon Mac) | `chromiumembedded/cef` @ `426920c33ef22551e0b8f48cf343d623ac25642d` (Chromium `156.0.8078.48` @ `d0c02bfa`) | none applied | `recipes/cef/macos` @ `44f2099` (arm64; x86_64 see note) |
| `8078-r2` | linux x86_64 | obs-rust (container) | `chromiumembedded/cef` @ `426920c33ef22551e0b8f48cf343d623ac25642d` | none applied | `recipes/cef/linux` @ `75bdc03` |

All `obs-studio-libs` commits are on its `main` branch.

---

## `7871` — mirror of an upstream archive (not built here)

Release published 2026-10-13. The six archives are **byte-for-byte copies of
the CEF 7871 archives that obs-studio fetches from
`https://cdn-fastly.obsproject.com/downloads`**; five of the six sha256s are
the hashes obs-rust/obs-studio's `CMakePresets.json` still pins for 7871. The
sixth, `cef_binary_7871_linux_x86_64.tar.xz`, is no longer pinned there: that
platform now builds from the `8078-r2` archive (`dc463c…`), so its 7871 hash is
recorded here alone. See obs-rust/obs-studio#614.

Because these are an upstream binary redistribution, there is no obs-rust
recipe and no upstream CEF commit recorded here: the CEF version is 7871
(Chromium 150), and the exact `chromiumembedded/cef` commit was chosen by
whoever built the upstream archives, not by this repository. Not reproducible
from here beyond the sha256.

| Asset | sha256 |
|---|---|
| `cef_binary_7871_linux_x86_64.tar.xz` | `8ffc64d06ea74da613eeda7f2aa5d4e98661af6fa3ed8c68724c9a6e4e89ea8e` |
| `cef_binary_7871_linux_aarch64.tar.xz` | `b05cf13b0f482e3b0149ac21268268c536d091b5e6608c742019ebd6c8e8a1e3` |
| `cef_binary_7871_macos_x86_64.tar.xz` | `980783bba509e7e79a671c29040bbe29d832a934c1368b500a17a22e076eb0f4` |
| `cef_binary_7871_macos_arm64.tar.xz` | `0343233fc462384c2f1f58a4222028bc6171f05948a822241f89df2faa7cee8d` |
| `cef_binary_7871_windows_x64.zip` | `54cd191a3f56bb1f05be22b95e1fa8041061b38bd5fe94e0776fb34dd599f341` |
| `cef_binary_7871_windows_arm64.zip` | `3f5b604eab71baab20c08f054d68c6212d06237e172fb8d38271c2384c2eb3a6` |

## `8078` — Linux x86_64 (built by obs-rust)

Release published 2026-10-23. Built from upstream CEF branch **8078** with
proprietary codecs (`proprietary_codecs=true ffmpeg_branding=Chrome`, so
H.264/AAC play in browser sources) and `symbol_level=0 chrome_pgo_phase=0`, so
this archive has no line tables; `8078-r2` below is the same CEF rebuilt with
`symbol_level=1` for sources.

- Upstream source: `chromiumembedded/cef` @
  `426920c33ef22551e0b8f48cf343d623ac25642d`
  (CEF reports `156.0.8+g426920c+chromium-156.0.8078.48`).
- Recipe: `recipes/cef/linux/build.sh` in obs-rust/obs-studio-libs @
  `e653bfb` (*"CEF 8078 Linux x86_64 recipe with proprietary codecs (#11)"*,
  2026-10-23).
- Patch commit in use: **none.** The recipe runs `automate-git.py` against
  upstream CEF only; it applies no branch of this repository.

| Asset | sha256 |
|---|---|
| `cef_binary_8078_linux_x86_64.tar.xz` | `fb36df11bc8280c9cb666732becc1fe267f833bb964dbb13d69cad33488171dc` |

## `8078` — macOS arm64 / x86_64 (built by @nathan-v)

Same upstream commit and codec flags. Both archives set `symbol_level=1`
(`is_official_build=true proprietary_codecs=true ffmpeg_branding=Chrome
symbol_level=1 chrome_pgo_phase=0`), read from the `args.gn` each build printed,
so line tables are present in the binaries. The dSYM directory was removed
before packing, so no separate debug-symbol archive is attached. The sandbox
library is kept in the framework. Built by **@nathan-v on his own Apple Silicon
Mac** (Xcode 27.0, 27A266a) and published here by the maintainer; hashes were
verified against the builder's report and the release's own digests, and the
archive contents were spot-checked (framework, `libcef_sandbox.dylib`,
`libcef_dll_wrapper.a`, per-archive Mach-O architecture). The builder's identity
was validated by the maintainer (obs-rust/obs-studio#2418).

- Upstream source: `chromiumembedded/cef` @
  `426920c33ef22551e0b8f48cf343d623ac25642d`; Chromium `refs/tags/156.0.8078.48`
  @ `d0c02bfad08ec92a6ffcf989e53cd995e6381a4c`.
- Recipe: `recipes/cef/macos/build.sh` in obs-rust/obs-studio-libs @ `44f2099`
  (*"CEF 8078 macOS recipe"*, #17, 2026-10-26) — the arm64 build's log
  environment matches that commit. See the note below for the x86_64 run.
- Patch commit in use: **none.** The recipe runs `automate-git.py` against
  upstream CEF only. The invocation is `--branch=8078
  --checkout=426920c33ef22551e0b8f48cf343d623ac25642d --no-chromium-history
  --no-debug-build --arm64-build|--x64-build --no-update --force-build
  --minimal-distrib --minimal-distrib-only --no-distrib-archive
  --build-target=cefclient`; the x86_64 run also exports `CEF_ENABLE_AMD64=1`
  and `GN_OUT_CONFIGS=Release_GN_x64`.
- Codesign: the arm64 framework and `libcef_sandbox.dylib` are ad-hoc
  linker-signed (`flags=0x20002(adhoc,linker-signed)`, `Signature=adhoc`,
  `VersionMin` 851968 = macOS 13.0, `VersionSDK` 1769472 = 27.0 SDK); the
  x86_64 framework, its inner binary and `libcef_sandbox.dylib` report `code
  object is not signed at all`. That asymmetry is ld64's arm64 requirement, not
  a skipped signing step.
- Version header: `include/cef_version.h` in both archives carries
  `CEF_SANDBOX_COMPAT_HASH ""`. That is the correct macOS header, not a value
  that failed to bake in: `cef/BUILD.gn`'s `make_version_header` action adds
  `--sandbox-files` only inside `if (is_win)`, `tools/make_version_header.py`
  substitutes `""` when it is given no sandbox files, and the only consumer of a
  non-empty value is the Windows bootstrap loader
  (`libcef_dll/wrapper/cef_scoped_library_loader_win.cc`). The macOS sandbox is
  `libcef_sandbox.dylib` inside the framework, which both archives carry, built
  with `CEF_USE_SANDBOX` (obs-rust/obs-studio#2418).
- These are the only published archives not built on obs-rust infrastructure,
  and they are pinned by hash rather than by an attested build run.

| Asset | sha256 |
|---|---|
| `cef_binary_8078_macos_arm64.tar.xz` | `2ddd78dd796af956728984747aaba4d859658354f00c6035be975b9f322f95cb` |
| `cef_binary_8078_macos_x86_64.tar.xz` | `75281c862a59321bf83735926efeafed20e816cfa07fcccdf20a2d1748c539d0` |

## `8078-r2` — Linux x86_64 with line tables (built by obs-rust)

Release published 2026-10-26. Same CEF as `8078`, rebuilt with
`symbol_level=1` so `libcef.so` carries `.debug_line` and GDB can resolve source
lines from the Flatpak Debug extension (obs-rust/obs-studio#2363).

- Upstream source: `chromiumembedded/cef` @
  `426920c33ef22551e0b8f48cf343d623ac25642d`.
- Recipe: `recipes/cef/linux/build.sh` in obs-rust/obs-studio-libs @
  `75bdc03` (*"cef: Build 8078 with line tables (symbol_level=1) (#13)"*,
  2026-10-25).
- Patch commit in use: **none.**
- Built by bosn jobs 23 (build) and 31 (pack).

| Asset | sha256 |
|---|---|
| `cef_binary_8078_linux_x86_64.tar.xz` | `dc463c360d4b8e4eb482fa78a5b92b996b053a4a8650dba3052c89390430ac9c` |

---

## What this record does not settle

Recorded here so the gap is visible, not hidden:

1. **The Linux recipe pins a branch, not a commit.** `recipes/cef/linux/build.sh`
   uses `--branch=8078` with no `--checkout`, so it follows the tip of the
   moving upstream branch. The commit actually built
   (`426920c33ef22551e0b8f48cf343d623ac25642d`) is known only because the CEF
   version string inside the archive names it. The macOS recipe pins
   `CHECKOUT=426920c…` explicitly and is reproducible from the recipe alone.
2. **The x86_64 macOS archive cannot be attributed to a committed recipe.**
   `recipes/cef/macos/` first appears at `44f2099` (2026-10-26, #17) and was
   revised at `5d80772` (#18). The arm64 build's log environment matches
   `44f2099`, and both archives were packed before #18 merged — yet the x86_64
   log prints `CEF_ENABLE_AMD64=1` and `GN_OUT_CONFIGS=Release_GN_x64`, exports
   that only appear in the recipe at `5d80772`. That run therefore used
   pre-merge (uncommitted) script content. The arm64 archive is attributed to
   `44f2099`; the x86_64 archive is recorded against `44f2099` as the closest
   named revision with that caveat.
3. **No published archive carries a patch from a branch of this repository,
   and none is needed — answered (#2674).** Every recipe calls
   `automate-git.py` and nothing else. The only header change the fork's
   branches make that `plugins/obs-browser` can consume is
   `CEF_ON_ACCELERATED_PAINT2` / `OnAcceleratedPaint2` (commit `8092a57fc`
   and the `on-accelerated-paint2`/`MediaHandler`/`hack-and-a-half`/
   `jim-system-freeze-workaround` branches). Checked against both shipped
   releases: the `7871` archive's `include/cef_render_handler.h` declares only
   upstream's `OnAcceleratedPaint` (CEF 6367+), and upstream
   `chromiumembedded/cef@426920c33ef22551e0b8f48cf343d623ac25642d` — the
   `8078` source — is the same. Neither defines `CEF_ON_ACCELERATED_PAINT2`
   nor declares `OnAcceleratedPaint2`, so the fork's patch is in neither
   archive. `7871` did not carry it either, so its absence from `8078` is not
   a regression. `obs-browser` at `434cc38` compiles its
   `OnAcceleratedPaint2` override only when that macro is defined, so both
   releases take the upstream shared-texture path. If the fork's patches are
   ever wanted in the shipped CEF, the recipe needs a pinned patch step
   against a commit here; until then this question is closed.
4. **No source tarball is attached, and none was retained — answered
   (#2674).** `automate-git.py` writes a `cef.tar.gz` into its build output.
   The `7871` archives are byte-for-byte copies of upstream OBS's published
   binaries, so no tarball was fetched or produced here. The `8078` Linux
   builds ran in bosn container jobs 23 (build) and 31 (pack) and the macOS
   builds on a contributor's machine; neither build output was retained, so no
   `cef.tar.gz` survives to attach. Publishing one now would require a
   rebuild, which this record deliberately does not claim.
