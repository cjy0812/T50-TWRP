# CLAUDE.md

本文件为 Claude Code (claude.ai/code) 在此仓库中工作时提供指导。

## ⚠️ 工作准则（必须遵守）

1. **禁止编造猜测答案** — 先搜索验证，其次本地文件核查，找不到明确答案时直接说"未确认"
2. **构建前必须检查** — 确认所有参数正确、文件已提交推送，无误后再触发构建
3. **优先本地读取，但保证同步** — 读本地文件做判断，但确保与 GitHub commit 一致
4. **合理使用 MCP 工具** — 需要搜索验证时使用，不要凭空猜测

## 仓库概述

这是一个 **Action-TWRP-Builder CI/CD 模板**（fork 自 azwhikaru/Action-TWRP-Builder）。它使用 GitHub Actions 自动构建 TWRP（Team Win Recovery Project）recovery 镜像。

**这不是传统代码库** — 它是一个构建自动化模板。没有需要本地编译的源代码；"构建"完全在 GitHub Actions runner 中进行。

## 构建命令

构建通过 GitHub Actions workflow dispatch 手动触发：

```bash
gh workflow run "Recovery Build.yml" \
  -f MANIFEST_URL="https://github.com/minimal-manifest-twrp/platform_manifest_twrp_aosp" \
  -f MANIFEST_BRANCH="twrp-12.1" \
  -f DEVICE_TREE_URL="<设备树仓库地址>" \
  -f DEVICE_TREE_BRANCH="<分支>" \
  -f DEVICE_PATH="device/<厂商>/<设备代号>" \
  -f DEVICE_NAME="<设备代号>" \
  -f MAKEFILE_NAME="twrp_<设备代号>" \
  -f BUILD_TARGET="vendorbootimage"
```

### 构建目标

| 目标 | 输出 | 适用场景 |
|------|------|---------|
| `bootimage` | `boot.img` | recovery-as-boot 方案的设备 |
| `recoveryimage` | `recovery.img` | 有独立 recovery 分区的设备 |
| `vendorbootimage` | `vendor_boot.img` | vendor_boot 方案的设备（MTK A/B） |

## 架构

### Workflow 步骤（`.github/workflows/Recovery Build.yml`）

1. **清理** - 释放不必要的软件包以释放磁盘空间
2. **环境设置** - 安装构建依赖、OpenJDK 11、repo 工具
3. **Manifest 同步** - 从 TWRP minimal manifest 执行 `repo init` + `repo sync`
4. **设备树克隆** - 克隆外部设备树仓库到 `DEVICE_PATH`
5. **依赖同步** - 通过 `convert.sh` 解析 `.dependencies` 文件并同步额外仓库
6. **构建** - `lunch <makefile>-eng && make <BUILD_TARGET>`
7. **发布** - 上传 `.img` 文件到 GitHub Releases

### 辅助脚本（`scripts/convert.sh`）

解析 TWRP/Omni 设备树的 `.dependencies` 文件，生成 `.repo/local_manifests/roomservice.xml` 供 `repo sync` 使用。

## 设备树要求

有效的 TWRP 设备树必须包含：
- `AndroidProducts.mk` - 定义 `PRODUCT_MAKEFILES` 和 `COMMON_LUNCH_CHOICES`
- `<makefile>.mk` - 产品定义（继承 vendor 配置 + 设备配置）
- `device.mk` - 设备特定包和配置
- `BoardConfig.mk` - 分区大小、内核配置、平台信息

### 常见 BoardConfig.mk 变量

```makefile
BOARD_USES_RECOVERY_AS_BOOT :=         # T50 不使用 boot.img 承载 recovery
BOARD_USES_VENDOR_BOOT := true         # recovery 在 vendor_boot 分区
BOARD_BOOT_HEADER_VERSION := 4
BOARD_BOOTIMAGE_PARTITION_SIZE := <大小>
BOARD_VENDOR_BOOTIMAGE_PARTITION_SIZE := <大小>
```

## 项目上下文

对于 T50 设备（tb8786p1_64_k510_wifi）：
- 设备树仓库：`cjy0812/T50-TWRP_device_tree`（分支：`twrp-12.1`）
- SoC：MediaTek MT6768
- 架构：A/B 分区 + vendor_boot 方案
- 原始 dump 文件：`G:\T50\Raw_dump`
- factory 系统：Android 14 / SDK 34 / `UP1A.231005.007`
- 没有独立 `recovery` 分区；默认构建目标使用 `vendorbootimage`

详见 `背景信息.md` 获取项目历史和设备规格。
