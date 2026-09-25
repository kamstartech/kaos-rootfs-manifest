# kaos-rootfs-manifest

`repo`-tool manifest for building the Kaos distro rootfs on a fresh machine
(Linux x86_64, Linux arm64, or macOS/Docker Desktop including Apple Silicon)
without checking out the full AOSP/LineageOS tree — `build-rootfs.sh` never
touches it.

## Setup

```bash
mkdir kaos-rootfs && cd kaos-rootfs
repo init -u https://github.com/kamstartech/kaos-rootfs-manifest.git -m manifest.xml
repo sync
git submodule update --init --recursive hybris/droid-configs/droid-configs-device
```

The `-m manifest.xml` is required — `repo init` defaults to looking for
`default.xml`, which this repo doesn't have.

kaos-configs, droid-config-perseus, android-headers, libhybris, and
libgbinder are private repos. Authenticate before `repo sync`:
```bash
gh auth login
gh auth setup-git
```

## Build

```bash
hybris/kaos-configs/build-rootfs.sh --distro kali   # or ubuntu, debian
```

Requires Docker. On macOS, `build-rootfs.sh` verifies arm64 execution works
under Docker Desktop's own VM automatically. On x86_64 Linux hosts it
registers the `qemu-aarch64` binfmt handler itself (best-effort) or tells
you the exact package/command to install it manually.
