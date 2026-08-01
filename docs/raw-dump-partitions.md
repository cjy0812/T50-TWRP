# T50 原始转储分区说明

本文档说明 T50 的分区转储。`G:\T50\Raw_dump` 是学习系统转储，`G:\T50\T50-aosp-zyyme-260716` 是当前设备 fastbootd 匹配的工厂底座；判断当前适配时以后者为准。

## 关键结论

- 该设备对启动关键分区采用 A/B 槽位设计。
- 转储中不存在独立的 `recovery` 分区。
- 该设备系列的回退资源位于 `vendor_boot` 中，因此 TWRP 构建目标应为 `vendorbootimage`，而非 `recoveryimage`。
- `boot_a.bin` 大小为 32 MiB，包含内核和一个较小的通用启动 ramdisk。
- `vendor_boot_a.bin` 大小为 64 MiB，包含供应商 ramdisk、第一阶段 fstab 文件、recovery rc 文件、DTB 和许多内核模块。
- 工厂包 `super.bin` 为 Android 14 / SDK 34，构建 ID 为 `UP1A.231005.007`，指纹为 `alps/.../mp1V814:userdebug/test-keys`。
- 当前设备 fastbootd fetch 的 `vendor_boot_a` 与 `G:\T50\T50-aosp-zyyme-260716\vendor_boot_a.bin` SHA256 一致。

## 槽位与分区映射

| 分区转储                                     |                 大小 | 对 TWRP 的意义                                                                           |
| -------------------------------------------- | -------------------: | ---------------------------------------------------------------------------------------- |
| `boot_a.bin`                                 |               32 MiB | A 槽启动镜像。包含内核和通用 ramdisk。请勿在此打包完整的 TWRP。                          |
| `boot_b.bin`                                 |               32 MiB | B 槽启动镜像占位/转储。                                                                  |
| `vendor_boot_a.bin`                          |               64 MiB | A 槽供应商启动镜像。生成回退设备树的正确来源。                                           |
| `vendor_boot_b.bin`                          |               64 MiB | B 槽供应商启动镜像占位/转储。                                                            |
| `init_boot_a.bin`、`init_boot_b.bin`         |             各 8 MiB | 以命名分区存在，但当前转储不以 Android 启动头开头。                                      |
| `dtbo_a.bin`、`dtbo_b.bin`                   |             各 8 MiB | 设备树叠加层镜像。                                                                       |
| `vbmeta_a.bin`、`vbmeta_b.bin`               |             各 8 MiB | 顶级 AVB 元数据。                                                                        |
| `vbmeta_system_a.bin`、`vbmeta_system_b.bin` |             各 8 MiB | 动态系统侧分区的 AVB 元数据。                                                            |
| `vbmeta_vendor_a.bin`、`vbmeta_vendor_b.bin` |       各约 11.75 MiB | 供应商侧分区的 AVB 元数据。                                                              |
| `super.bin`                                  |               10 GiB | `system`、`system_ext`、`product` 和 `vendor` 的动态分区容器。                           |
| `metadata.bin` / `md_udc.bin`                | 32 MiB / 约 22.6 MiB | Android/TWRP 使用的元数据风格分区；`recovery.fstab` 当前将 `/metadata` 映射到 `md_udc`。 |
| `userdata`                                   |   未作为完整镜像转储 | 用户数据分区在 fstab 中被引用，但此处没有对应的 `userdata.bin` 完整镜像。                |

## 为什么没有 `recovery.img`

旧设备通常有一个物理 `recovery` 分区。本次转储没有。因此构建 `recoveryimage` 会为设备上不存在的分区创建镜像，并且还会应用 32 MiB 的大小限制，无法容纳生成的 TWRP ramdisk。

正确的做法是在 `vendor_boot` 头部 v4 内部构建或替换回退 ramdisk：

```bash
make vendorbootimage -j$(nproc --all)
```

Android 12+ fastboot 还支持在 fastboot 实现允许的设备上进行命名供应商 ramdisk 替换：

```bash
fastboot flash vendor_boot:recovery recovery.cpio.gz
```

对于 CI 发布，建议上传 `vendor_boot.img` 和 `recovery.cpio.gz`，不要将 `recovery.img` 视为 T50 的有效工件。当前更推荐后续基于工厂包 `vendor_boot_a.bin` 做离线 repack，而不是整包刷 CI 生成的 `vendor_boot.img`。

## 原始转储证据

工厂包 `boot_a.bin` 以 `ANDROID!` 开头，启动头部 v4，内核大小约 20.0 MiB，ramdisk 大小约 1.7 MiB。

工厂包 `vendor_boot_a.bin` 以 `VNDRBOOT` 开头，供应商启动头部 v4，包含一个平台 ramdisk 片段、一个约 156 KiB 的 DTB，分区大小为 64 MiB。其 ramdisk 包含：

- `first_stage_ramdisk/fstab.mt6768`
- `first_stage_ramdisk/fstab.mt8786`
- `init.recovery.mt6768.rc`
- `init.recovery.mt8786.rc`
- `lib/modules/*.ko`

工厂包 `super.bin` 包含 Android 14 产品属性：

```properties
ro.product.build.fingerprint=alps/sys_mssi_t_64_cn_wifi/mssi_t_64_cn_wifi:14/UP1A.231005.007/mp1V814:userdebug/test-keys
ro.product.build.version.release=14
ro.product.build.version.sdk=34
ro.product.ab_ota_partitions=boot,product,system,system_ext,vendor
```

## 设备树影响

设备树应使用构建系统实际能识别的 Android 构建变量名：

```makefile
BOARD_BOOT_HEADER_VERSION := 4
BOARD_VENDOR_BOOTIMAGE_PARTITION_SIZE := 67108864
BOARD_MOVE_RECOVERY_RESOURCES_TO_VENDOR_BOOT := true
BOARD_INCLUDE_RECOVERY_RAMDISK_IN_VENDOR_BOOT := true
PRODUCT_BUILD_VENDOR_BOOT_IMAGE := true
```

不要依赖诸如 `BOARD_BOOTIMG_HEADER_VERSION` 或 `BOARD_VENDORBOOTIMAGE_PARTITION_SIZE` 这类生成的名称；它们可能导致 `vendorbootimage` 没有实际的输出目标。
