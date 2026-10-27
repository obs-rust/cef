# Source provenance for the published CEF archives

This repository publishes **prebuilt** CEF binary archives under
[Releases](https://github.com/obs-rust/cef/releases). CEF is BSD-3-licensed, so
nothing here is required by licence — this file exists because a hash pin with
no named source is a pin you cannot repeat.

For every published archive it records the upstream CEF commit it was built
from, the recipe (repo + commit) that produced it, whether any patch from a
branch of *this* repository is applied, and the archive's sha256. A release tag
in this repository is only a GitHub-required target and carries no build
information; the source of truth is this file and the release body.

## Summary

| Release | Assets | Built by | Upstream CEF source | Patch commit | Recipe (obs-rust/obs-studio-libs) |
|---|---|---|---|---|---|
| `7871` | linux x86_64/aarch64, macOS x86_64/arm64, windows x64/arm64 | upstream OBS (mirror) | CEF 7871 / Chromium 150 — upstream binary, commit not recorded | none applied | none (mirror) |
| `8078` | linux x86_64 | obs-rust (container) | `chromiumembedded/cef` @ `426920c33ef22551e0b8f48cf343d623ac25642d` (`156.0.8+g426920c+chromium-156.0.8078.48`) | none applied | `recipes/cef/linux` @ `e653bfb` |
| `8078` | macOS arm64, macOS x86_64 | @nathan-v (own Apple Silicon Mac) | `chromiumembedded/cef` @ `426920c33ef22551e0b8f48cf343d623ac25642d` | none applied | `recipes/cef/macos` (see note) |
| `8078-r2` | linux x86_64 | obs-rust (container) | `chromiumembedded/cef` @ `426920c33ef22551e0b8f48cf343d623ac25642d` | none applied | `recipes/cef/linux` @ `75bdc03` |

All `obs-studio-libs` commits are on its `main` branch.

---

## `7871` — mirror of an upstream archive (not built here)

Release published 2026-10-13. The six archives are **byte-for-byte copies of
the CEF 7871 archives that obs-studio fetches from
`https://cdn-fastly.obsproject.com/downloads`**; each sha256 matches the hash
pinned in obs-rust/obs-studio's `CMakePresets.json`. See obs-rust/obs-studio#614.

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
H.264/AAC play in browser sources) and `symbol_level=1 chrome_pgo_phase=0`.

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

Same upstream commit and codec flags, `symbol_level=1 chrome_pgo_phase=0`; the
sandbox library is kept in the framework. Built by **@nathan-v on his own Apple
Silicon Mac** and published here by the maintainer; hashes were verified against
the builder's report and the archive contents were spot-checked (framework,
`libcef_sandbox.dylib`, `libcef_dll_wrapper.a`, per-archive Mach-O
architecture). The builder's identity was validated by the maintainer
(obs-rust/obs-studio#2418).

- Upstream source: `chromiumembedded/cef` @
  `426920c33ef22551e0b8f48cf343d623ac25642d`.
- Recipe: `recipes/cef/macos/build.sh` in obs-rust/obs-studio-libs — see the
  note below; it was committed after these archives were built.
- Patch commit in use: **none.** The recipe runs `automate-git.py` against
  upstream CEF only.
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
2. **The macOS recipe postdates the macOS archives.** `recipes/cef/macos/`
   first appears at `44f2099` (2026-10-26, #17) and was revised at `5d80772`
   (#18), while the `8078` macOS archives were built by @nathan-v from his
   working recipe of that time. The archives therefore cannot be attributed to
   a specific `obs-studio-libs` commit; the recipe at `5d80772` is the closest
   named revision and is recorded above as such.
3. **No published archive carries a patch from a branch of this repository.**
   Every recipe calls `automate-git.py` and nothing else. If the OBS-specific
   patches that this fork exists for must be present in the shipped CEF, the
   recipe needs a pinned patch step against a commit here; if they are not
   needed, that should be stated and this question closed
   (obs-rust/obs-studio#2674).
4. **No source tarball is attached.** `automate-git.py` writes a `cef.tar.gz`
   for the build; whether it still exists for any published build is not
   recorded here.
