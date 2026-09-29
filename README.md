# charly-fedora

The Fedora package repository for [charly](https://github.com/opencharly/charly) — the OpenCharly CLI and its composed toolchain, packaged as `.rpm` for `amd64` and `arm64`.

This repo owns the artifact **and the R10 bed that proves it**: the
`check-fedora-repo` deploy boots a disposable Fedora Cloud 43 VM, adds the
published charly dnf repo, installs the packaged `charly`, and asserts the
installed binary's version equals the version the package manager recorded.

## Add the repository

Create `/etc/yum.repos.d/charly.repo`:

```ini
[charly]
name=charly
baseurl=https://opencharly.github.io/charly-fedora/amd64
enabled=1
gpgcheck=1
gpgkey=https://opencharly.github.io/charly-fedora/RPM-GPG-KEY-charly
```

Then:

```sh
dnf install charly
```

For `arm64` hosts, use `baseurl=https://opencharly.github.io/charly-fedora/arm64`.

## Direct install

Download the `.rpm` for your architecture and install it with `dnf install`:

- amd64: `https://opencharly.github.io/charly-fedora/amd64/charly-amd64.rpm`
- arm64: `https://opencharly.github.io/charly-fedora/arm64/charly-arm64.rpm`

## Variants

| Package | Plugin set |
|---|---|
| `charly` | secrets, feature, vm, doctor, clean, settings, candy, mcp, review, pipeline (10) |
| `charly-full` | the default set + udev, preempt (12) |
| `charly-minimal` | doctor, clean, settings (3) |

## Triggering a build

The build workflow is manual: **Actions → build → Run workflow**, entering the
charly release CalVer to package (e.g. `2026.227.1026`). The main repo's release
is the source of truth for the binary, the plugins, and the packaging metadata.
Each build assembles the repo for both `amd64` and `arm64`, signs the RPMs and
the `repomd.xml` metadata, and install-tests the result before deploying to
GitHub Pages.

## Verification

- **CI install-test** (inside the build workflow): installs `charly` from a
  local `file://` mount of the assembled repo with `gpgcheck=1`, asserts
  `charly version` equals the packaged release, asserts the default-variant
  `plugin-<word>` set is served by the shared `charly-lib` host, and runs
  `charly doctor` from a non-project directory.
- **R10 bed** `check-fedora-repo`: `charly check run check-fedora-repo` boots
  the disposable Fedora Cloud 43 VM, installs the packaged `charly` from the
  PUBLISHED repo, and asserts the version match plus project-less plugin
  dispatch.

## Layout

- `charly.yml` — the `fedora-repo-vm` `kind: vm` template and the
  `check-fedora-repo` bed.
- `.github/workflows/build.yml` — the manual package build + Pages deploy.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `RPM-GPG-KEY-charly` — the RPM repo signing key.
- `index.html` — the Pages landing page.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-tools:charly` — the charly binary and its per-distro package repos.
- Fedora distro: `/charly-distros:fedora` — the Fedora base box and vocabulary.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder.
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella.
