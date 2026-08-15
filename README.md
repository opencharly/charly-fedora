# charly-fedora

The Fedora package repository for [charly](https://github.com/opencharly/charly) — the OpenCharly CLI and its composed toolchain, packaged as `.rpm` for `amd64` and `arm64`.

The repository is built by a GitHub Actions workflow (manual dispatch with a charly release CalVer) and published to GitHub Pages. Each build produces the `charly` package plus the named variants `charly-full` and `charly-minimal` (differing in the baked-in plugin set), signs the RPMs and the `repomd.xml` metadata, and install-tests the result before deploying.

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
| `charly` | secrets, feature, vm, doctor, clean, settings, candy |
| `charly-full` | the default set + udev, preempt |
| `charly-minimal` | doctor, clean, settings |

## Triggering a build

The workflow is manual: **Actions → build → Run workflow**, entering the charly release CalVer to package (e.g. `2026.227.1026`). The main repo's release is the source of truth for the binary, the plugins, and the packaging metadata.

## Verification

Each build install-tests the packages from a local `file://` mount of the assembled repository before deploying: it installs `charly` with `gpgcheck=1`, asserts `charly version` equals the packaged release, and runs `charly doctor` from a non-project directory to prove the baked plugins dispatch project-less.
