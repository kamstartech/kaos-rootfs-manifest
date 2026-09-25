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

## mesa-freedreno (not repo-synced — it's a build product, not source)

`hybris/mw/mesa-freedreno` is a prebuilt Mesa/freedreno driver stack
(cross-compiled for the `aarch64-meego-linux-gnu` target), used by
`build-rootfs.sh` for direct-KMS GPU rendering (`libEGL`, `libgbm`,
`libGLESv2`, `libgallium_dri.so`, `libvulkan_freedreno.so`). It's a build
artifact, not a git-trackable source tree — fetch it instead:

```bash
mkdir -p hybris/mw/mesa-freedreno
curl -o hybris/mw/mesa-freedreno/mesa-freedreno-25.1.4.tar.bz2 \
  https://kamstar.tech/sailfishos/updates/perseus/build-deps/mesa-freedreno-25.1.4.tar.bz2
tar xf hybris/mw/mesa-freedreno/mesa-freedreno-25.1.4.tar.bz2 -C hybris/mw/mesa-freedreno
```

Then run `hybris/kaos-configs/build-mesa-kgsl.sh` to produce the
`aarch64-meego-linux-gnu/` build output `build-rootfs.sh` actually mounts.

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
