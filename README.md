# kaos-rootfs-manifest

`repo`-tool manifest for building the Kaos distro rootfs on a fresh machine
(Linux x86_64, Linux arm64, or macOS/Docker Desktop including Apple Silicon)
without checking out the full AOSP/LineageOS tree — `build-rootfs.sh` never
touches it.

## Setup

```bash
mkdir kaos-rootfs && cd kaos-rootfs
repo init -u https://github.com/kamstartech/kaos-rootfs-manifest.git
repo sync
git submodule update --init --recursive hybris/droid-configs/droid-configs-device
```

## Build

```bash
hybris/kaos-configs/build-rootfs.sh <distro>   # e.g. kali, ubuntu
```

Requires Docker. On macOS, `build-rootfs.sh` verifies arm64 execution works
under Docker Desktop's own VM automatically. On x86_64 Linux hosts it
registers the `qemu-aarch64` binfmt handler itself (best-effort) or tells
you the exact package/command to install it manually.

## Known gaps not covered by this manifest

- `hybris/mw/libhybris/libhybris` currently has local, **unpushed** commits
  and uncommitted changes on this machine beyond what `kamstartech/libhybris`
  master has — a fresh `repo sync` elsewhere will NOT get those. Push/commit
  them first if they're meant to be part of every build.
- `hybris/mw/libgbinder` and `hybris/mw/wlroots-hwcomposer` also have local
  uncommitted changes on this machine not yet pushed to their remotes.
