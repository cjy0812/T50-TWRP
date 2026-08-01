# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is an **Action-TWRP-Builder CI/CD template** (forked from azwhikaru/Action-TWRP-Builder). It automates building TWRP (Team Win Recovery Project) recovery images using GitHub Actions.

**This is NOT a traditional codebase** — it's a build automation template. There is no source code to compile locally; the "build" happens entirely in GitHub Actions runners.

## Build Command

The build is triggered manually via GitHub Actions workflow dispatch:

```bash
gh workflow run "Recovery Build.yml" \
  -f MANIFEST_URL="https://github.com/minimal-manifest-twrp/platform_manifest_twrp_aosp" \
  -f MANIFEST_BRANCH="twrp-12.1" \
  -f DEVICE_TREE_URL="<device-tree-repo-url>" \
  -f DEVICE_TREE_BRANCH="<branch>" \
  -f DEVICE_PATH="device/<vendor>/<codename>" \
  -f DEVICE_NAME="<codename>" \
  -f MAKEFILE_NAME="twrp_<codename>" \
  -f BUILD_TARGET="bootimage"
```

### Build Targets

| Target | Output | Use Case |
|--------|--------|----------|
| `bootimage` | `boot.img` | Devices with recovery-as-boot scheme |
| `recoveryimage` | `recovery.img` | Devices with separate recovery partition |
| `vendorbootimage` | `vendorboot.img` | Devices with vendor_boot scheme (MTK A/B) |

## Architecture

### Workflow Steps (`.github/workflows/Recovery Build.yml`)

1. **Cleanup** - Remove unnecessary packages to free disk space
2. **Environment Setup** - Install build dependencies, OpenJDK 11, repo tool
3. **Manifest Sync** - `repo init` + `repo sync` from TWRP minimal manifest
4. **Device Tree Clone** - Clone external device tree repo to `DEVICE_PATH`
5. **Dependency Sync** - Parse `.dependencies` file via `convert.sh` and sync additional repos
6. **Build** - `lunch <makefile>-eng && make <BUILD_TARGET>`
7. **Release** - Upload `.img` files to GitHub Releases

### Helper Script (`scripts/convert.sh`)

Parses TWRP/Omni device tree `.dependencies` files and generates `.repo/local_manifests/roomservice.xml` for `repo sync`.

## Device Tree Requirements

A valid TWRP device tree must contain:
- `AndroidProducts.mk` - Defines `PRODUCT_MAKEFILES` and `COMMON_LUNCH_CHOICES`
- `<makefile>.mk` - Product definition (inherits vendor config + device config)
- `device.mk` - Device-specific packages and configurations
- `BoardConfig.mk` - Partition sizes, kernel config, platform info

### Common BoardConfig.mk Variables

```makefile
BOARD_USES_RECOVERY_AS_BOOT := true    # Recovery packed into boot.img
BOARD_USES_VENDOR_BOOT := true         # Recovery in vendor_boot partition
BOARD_BOOTIMAGE_PARTITION_SIZE := <size>
BOARD_VENDORBOOTIMAGE_PARTITION_SIZE := <size>
```

## Project Context

For the T50 device (tb8786p1_64_k510_wifi):
- Device tree repo: `cjy0812/T50-TWRP_device_tree` (branch: `twrp-12.1`)
- SoC: MediaTek MT6768
- Architecture: A/B partitioning with vendor_boot scheme
- Raw dump files: `G:\T50\Raw_dump`

See `背景信息.md` for detailed project history and device specifications.
