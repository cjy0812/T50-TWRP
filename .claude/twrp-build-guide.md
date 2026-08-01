# TWRP 构建指南与设备适配

## ⚠️ 工作准则（必须遵守）

1. **禁止编造猜测答案** — 先搜索验证，其次本地文件核查，找不到明确答案时直接说"未确认"
2. **构建前必须检查** — 确认所有参数正确、文件已提交推送，无误后再触发构建
3. **优先本地读取，但保证同步** — 读本地文件做判断，但确保与 GitHub commit 一致
4. **合理使用 MCP 工具** — 需要搜索验证时使用，不要凭空猜测

---

## 设备信息

| 项目 | 值 | 状态 |
|------|-----|------|
| 设备代号 | tb8786p1_64_k510_wifi | ✅ 已验证 |
| 品牌 | ZYB | ✅ 已验证 |
| 型号 | ZPD1321 | ✅ 已验证 |
| SoC | MediaTek MT6768 (Helio P65) | ✅ 已验证 |
| 架构 | arm64 (armv8-a) | ✅ 已验证 |
| 分区方案 | A/B + vendor_boot | ⚠️ 待确认（见下方） |

## 仓库信息

| 仓库 | URL | 分支 | 说明 |
|------|-----|------|------|
| Builder | cjy0812/T50-TWRP | main | CI/CD workflow 模板 |
| 设备树 | cjy0812/T50-TWRP_device_tree | twrp-12.1 | 设备特定配置 |

## 分区布局（来自 dump 文件）

| 分区 | 实际大小 | 说明 |
|------|---------|------|
| boot_a / boot_b | **32 MB** | kernel + boot ramdisk + dtb |
| vendor_boot_a / vendor_boot_b | **64 MB** | vendor ramdisk |
| init_boot_a / init_boot_b | 8 MB | initial ramdisk |
| dtbo_a / dtbo_b | 8 MB | Device Tree Blob Overlay |
| super | 10 GB | 动态分区 (system/vendor/product/system_ext) |
| modem (md1img) | 128 MB | 基带固件 |

**关键事实**:
- 设备树生成器使用 `vendor_boot_a.bin`（64MB）成功生成（用 `boot_a.bin` 失败）
- 设备**没有独立 recovery 分区**

## BoardConfig.mk 配置

### 当前配置（已推送到远程）

```makefile
# 分区大小
BOARD_BOOTIMAGE_PARTITION_SIZE := 33554432  # 32 MB
BOARD_RECOVERYIMAGE_PARTITION_SIZE := 33554432  # 32 MB
BOARD_VENDORBOOTIMAGE_PARTITION_SIZE := 67108864  # 64 MB

# Boot 方案
BOARD_USES_RECOVERY_AS_BOOT :=  # 已移除
BOARD_USES_VENDOR_BOOT := true

# Kernel（预编译）
TARGET_FORCE_PREBUILT_KERNEL := true
TARGET_PREBUILT_KERNEL := $(DEVICE_PATH)/prebuilt/kernel
TARGET_PREBUILT_DTB := $(DEVICE_PATH)/prebuilt/dtb.img
BOARD_BOOTIMG_HEADER_VERSION := 4

# 平台
TARGET_BOARD_PLATFORM := mt6768
TARGET_KERNEL_CONFIG := tb8786p1_64_k510_wifi_defconfig
```

### 预编译文件

| 文件 | 大小 | 说明 |
|------|------|------|
| prebuilt/kernel | 19.27 MB | gzip 压缩内核 |
| prebuilt/dtb.img | ~156 KB | 设备树二进制 |

---

## ⚠️ 待解决问题：正确的构建目标

**问题**: `vendorbootimage` 不是有效的 ninja 目标（构建报 `ninja: no work to do`）

**已验证无效的目标**:
- `vendorbootimage` → ninja: no work to do
- `vendorboot` → ninja: unknown target
- `bootimage` → 成功但 boot.img 64MB（超出实际 boot 分区 32MB）

**待搜索确认**:
- 在 TWRP 12.1 构建系统中，vendor_boot 镜像的正确构建目标是什么？
- 是否需要先构建 `recoveryimage` 再手动用 mkbootimg 打包？

**当前状态**: 正在搜索验证中...

---

## 参考资源

- 原始 dump 文件: `G:\T50\Raw_dump`
- TWRP 版本参考: `TWRP-Ver.md`
- OrangeFox 版本参考: `OFRP-Ver.md`
