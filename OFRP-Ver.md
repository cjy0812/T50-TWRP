# ------------------------------------
#
# OrangeFox Recovery（fox_12.1分支）的自定义构建变量
#
# 这些构建变量应在构建前声明——可以在shell脚本（例如“vendorsetup.sh”）中，或在命令行中声明
#
# 版权所有 (C) 2019-2025 OrangeFox Recovery项目
# 日期：2025年8月3日
#
# -----------------------------------
#

**"TARGET_ARCH"**
  - 根据设备是32位还是64位，将其设置为“arm”或“arm64”
  - 例如：“export TARGET_ARCH=arm”
  - 默认值 = arm64


**"FOX_AB_DEVICE"**（从“OF_AB_DEVICE”重命名）
   - 标识设备是否为A/B设备
   - 如果设备是A/B设备，设置为1（**务必确认设备确实是A/B设备**）
   - 默认值 = 0


**"OF_AB_DEVICE_WITH_RECOVERY_PARTITION"** [新增]
   - 如果设备是具有专用恢复分区的A/B设备，设置为1（**务必确认设备确实如此**）
   - 默认值 = 0


**"OF_RECOVERY_AB_FULL_REFLASH_RAMDISK"** [新增]
   - 设置为1可触发将实时恢复内存磁盘（而非恢复分区）刷入两个插槽
   - 仅当“OF_AB_DEVICE_WITH_RECOVERY_PARTITION”也启用时才有用
   - 使用时需谨慎！
   - 如果启用此变量，它将适用于“刷入当前OrangeFox”和“刷入ROM后重新刷入OrangeFox”
     （此过程将花费更多时间，但更不容易因非标准ROM安装程序覆盖恢复分区而受影响）
   - 默认值 = 0


**"FOX_PORTS_TMP"**
   - 指向用于创建zip安装程序的自定义临时目录
   - 确保该目录具有写入权限
   - 无默认值


**"FOX_PORTS_INSTALLER"**
   - 指向用于修改/附加安装程序文件的自定义目录
   - 创建zip安装程序前，该目录内容将被直接复制


**"FOX_LOCAL_CALLBACK_SCRIPT"**
   - 指向自定义“回调”脚本，该脚本将在创建最终恢复镜像前执行
   - 例如，用于删除某些文件或向内存磁盘添加某些文件的脚本
   - 确保设置脚本的可执行权限（例如：“chmod 0755 my-callback-script.sh”）
   - 无默认值


**"FOX_REPLACE_TOOLBOX_GETPROP"**
   - 设置为1可替换“getprop”命令的（精简版）toolbox版本
   - 如果定义了此变量，toolbox的“getprop”命令将被更完整的版本（resetprop）替换
   - 默认值 = 0


**"FOX_RECOVERY_INSTALL_PARTITION"**
   - !!! 通常情况下应保持默认，不要修改 !!!
   - 仅当设备的恢复分区位置与默认的“/dev/block/by-name/recovery”不同时才设置此变量
   - 默认值 = "/dev/block/by-name/recovery"


**"FOX_RECOVERY_SYSTEM_PARTITION"**
   - !!! 通常情况下应保持默认，不要修改 !!!
   - 仅当设备的系统分区位置与默认的“/dev/block/by-name/system”不同时才设置此变量
     （例如，某些三星设备的系统分区位置不同）
   - 默认值 = "/dev/block/by-name/system"


**"FOX_RECOVERY_VENDOR_PARTITION"**
   - !!! 通常情况下应保持默认，不要修改 !!!
   - 仅当设备的vendor分区位置与默认的“/dev/block/by-name/vendor”不同时才设置此变量
     （例如，某些三星设备的vendor分区位置不同）
   - 默认值 = "/dev/block/by-name/vendor"


**"FOX_RECOVERY_VENDOR_BOOT_PARTITION"** [新增]
   - 仅用于vendor_boot构建；通常情况下应保持默认，不要修改 !!!
   - 仅当设备的vendor_boot分区位置与默认的“/dev/block/by-name/vendor_boot”不同时才设置此变量
   - 默认值 = "/dev/block/by-name/vendor_boot"


**"FOX_RECOVERY_BOOT_PARTITION"** [仅R11.1及更高版本]
   - !!! 通常情况下应保持默认，不要修改 !!!
   - 仅当设备的boot分区位置与默认的“/dev/block/by-name/boot”不同时才设置此变量
     （例如，某些设备的boot分区位置不同）
   - 默认值 = "/dev/block/by-name/boot"


**"OF_USE_LZMA_COMPRESSION"**（从“FOX_USE_LZMA_COMPRESSION”重命名）
   - 如果希望对内存磁盘使用（速度慢但压缩效果更好的）lzma压缩，设置为1；
   - * 这要求构建系统中具有最新的lzma二进制文件，并且
   - * 在BoardConfig中设置（如果不自行设置，将自动设置）：
   -     BOARD_RAMDISK_USE_LZMA := true
   - * 内核还必须内置lzma压缩支持
   - 默认值 = 0（即使用标准gzip压缩，速度快但压缩效果较差）


**"OF_USE_LZ4_COMPRESSION"**（从“FOX_USE_LZ4_COMPRESSION”重命名）
   - 若（因某种原因）希望对内存磁盘使用lz4压缩，设置为1；
   - * 这要求构建系统中具有最新的lz4二进制文件，并且
   - * 在BoardConfig中设置（如果不自行设置，将自动设置）：
   -     BOARD_RAMDISK_USE_LZ4 := true
   - * 内核还必须内置lz4压缩支持
   - 默认值 = 0（即使用标准gzip压缩，压缩效果更好）


**"FOX_USE_TAR_BINARY"**
   - 设置为1可添加gnu tar二进制文件（/sbin/gnutar）
   - 必须在构建前在shell脚本或命令行中设置
   - 这将使恢复镜像大小增加约420kb
   - 默认值 = 0


**"FOX_USE_SED_BINARY"**
   - 设置为1可添加gnu sed二进制文件（/sbin/gnused）
   - 必须在构建前在shell脚本或命令行中设置
   - 这将使恢复镜像大小增加约200kb
   - 默认值 = 0


**"FOX_USE_LZ4_BINARY"** [新增]
   - 设置为1可添加预编译的lz4二进制文件（/sbin/lz4）
   - 必须在构建前在shell脚本或命令行中设置
   - 默认值 = 0


**"FOX_USE_ZSTD_BINARY"** [新增]
   - 设置为1可添加zstd二进制文件（/sbin/zstd）
   - 必须在构建前在shell脚本或命令行中设置
   - 默认值 = 0


**"FOX_USE_DATE_BINARY"**
   - 设置为1可添加gnu date二进制文件（/sbin/gnudate）
   - 必须在构建前在shell脚本或命令行中设置
   - 这将使恢复镜像大小增加约80kb（arm32为420kb）
   - 默认值 = 0


**"FOX_USE_RESETPROP_BINARY"** [已过时]


**"FOX_USE_BASH_SHELL"**
   - 设置为1可将bash设为默认shell，而非“sh”
   - 默认值 = 0
   - 如果不设置，bash仍会被复制，但不会替代“sh”


**"FOX_ASH_IS_BASH"**
   - 设置为1可使bash同时以“ash”名称可用
   - 默认值 = 0
   - 如果不设置，bash仍会被复制，但不会创建“ash”符号链接


**"FOX_REMOVE_BASH"**
   - 设置为1可从恢复中完全移除bash
   - 默认值 = 0


**"OF_USE_MAGISKBOOT"** [现在所有情况下均自动启用！]
   - 设置为1可使用magiskboot修补ROM的boot镜像


**"OF_USE_MAGISKBOOT_FOR_ALL_PATCHES"** [现在所有情况下均自动启用！]
   - 设置为1可使用magiskboot修补所有boot镜像和恢复镜像


**"OF_FORCE_MAGISKBOOT_BOOT_PATCH_MIUI"** [遗留]
   - 当安装MIUI ROM或在MIUI ROM上安装OrangeFox zip时，设置为1可强制使用magiskboot修补ROM的boot镜像——目的是防止MIUI用其自带的原生恢复覆盖OrangeFox
   - 默认值 = 0


**"FOX_DISABLE_UPDATEZIP"**（从“OF_DISABLE_UPDATEZIP”重命名）
   - 设置为1可禁用恢复zip的创建
   - 默认值 = 0


**"OF_DONT_PATCH_ON_FRESH_INSTALLATION"** [遗留]
   - 设置为1可防止在刷入OrangeFox zip时修补dm-verity和强制加密
   - 默认值 = 0
   - 如果ROM中启用了dm-verity，且此变量开启，可能会导致开机循环


**"OF_TWRP_COMPATIBILITY_MODE"**；**"OF_DISABLE_MIUI_SPECIFIC_FEATURES"**
   - 将其中任何一个设置为1可启用原生TWRP兼容模式
   - 在此模式下，MIUI OTA将被禁用
   - 默认值 = 0


**"OF_DONT_PATCH_ENCRYPTED_DEVICE"** [现在始终启用]


**"OF_KEEP_DM_VERITY"**；[现在始终启用]


**"OF_KEEP_FORCED_ENCRYPTION"**；[现在始终启用]


**"OF_KEEP_DM_VERITY_FORCED_ENCRYPTION"**；[现在始终启用]


**"OF_FORCE_DISABLE_DM_VERITY"**；[已过时]


**"OF_FORCE_DISABLE_FORCED_ENCRYPTION"**；[已过时]


**"OF_FORCE_DISABLE_DM_VERITY_FORCED_ENCRYPTION"**；[已过时]


**"OF_DISABLE_DM_VERITY"**；[已过时]


**"OF_DISABLE_FORCED_ENCRYPTION"**；[已过时]


**"OF_DISABLE_DM_VERITY_FORCED_ENCRYPTION"**；[已过时]


**"OF_CLASSIC_LEDS_FUNCTION"**
   - 设置为1可使用旧版R9.x的LED功能
   - 默认值 = 0


**"OF_SKIP_FBE_DECRYPTION"**
   - 设置为1可跳过FBE解密程序（防止在Fox图标或红米/小米图标处卡住）
   - 默认值 = 0


**"OF_SKIP_FBE_DECRYPTION_SDKVERSION"**
   - 此变量允许指示OrangeFox避免尝试解密具有特定Android SDK/API版本（及更高版本）的ROM
   - 设置为应跳过的Android SDK/API级别。需确保提供的值合理且正确
   - 当触发跳过逻辑时，日志屏幕将显示警告，且不会尝试解密数据分区
   - 例如：“export OF_SKIP_FBE_DECRYPTION_SDKVERSION=31”（即不尝试解密Android 12（SDK/API 31）及更高版本）
   - 有效的SDK/API级别可在以下位置查看：“https://source.android.com/setup/start/build-numbers”
   - 注意：如果使用此变量，请勿使用“OF_SKIP_FBE_DECRYPTION”，因为它会覆盖此变量
   - 默认值 = 无（即尝试解密所有Android API级别）


**"OF_OTA_RES_DECRYPT"** [已过时]
   - 设置为1可在MIUI OTA恢复期间尝试解密内部存储（而非报错退出）
   - 默认值 = 0


**"OF_NO_MIUI_OTA_VENDOR_BACKUP"** [遗留]
   - 设置为1可在准备增量MIUI OTA时（刷入MIUI ROM时）阻止备份vendor_image
   - 新（2019+）小米设备（如lavender、violet、raphael、ginkgo等）需要此类备份
   - 默认值 = 0


**"OF_NO_RELOAD_AFTER_DECRYPTION"**
   - 设置为1可防止OrangeFox在解密后重新运行启动过程
   - 默认值 = 0


**"OF_NO_TREBLE_COMPATIBILITY_CHECK"**
   - 设置为1可禁用对ROM中compatibility.zip的检查
   - 默认值 = 0


**"FOX_RESET_SETTINGS"**
   - 设置为“disabled”可在安装OrangeFox zip后不将OrangeFox设置重置为默认值
   - 不建议禁用此功能
   - 默认值 = 1


**"FOX_DELETE_AROMAFM"**
   - 设置为1可从zip安装程序中删除AromaFM（适用于AromaFM无法工作的设备）
   - 默认值 = 0


**"OF_USE_HEXDUMP"** [已过时]
   - 设置为1可使用外部hexdump工具获取文件头信息（而非内部代码）
   - 默认值 = 0


**"OF_USE_GREEN_LED"**
   - 设置为0可移除“绿色LED”设置
   - 默认值 = 1


**"OF_FLASHLIGHT_ENABLE"**
  - 标识是否启用手电筒功能
  - 默认值为“1”


**"OF_FL_PATH1"** 和 **"OF_FL_PATH2"**
  - 设置自定义手电筒路径（如果手电筒无法工作）
  - 例如：OF_FL_PATH1="/sys/class/leds/led_torch_2"


**"FOX_VERSION"** [已过时！！]
  - 此变量已过时。OrangeFox版本号（如“R11.2”、“R11.4”等）现在自动确定
  - 如果希望在版本号中添加自己的维护者版本信息，使用“FOX_MAINTAINER_PATCH_VERSION”（见下文）


**"FOX_MAINTAINER_PATCH_VERSION"** [新增]
  - 用于在OrangeFox版本号中添加维护者版本信息（如果需要）
  - 提供的值不应以任何分隔符开头（会自动添加下划线）
  - 例如：export FOX_MAINTAINER_PATCH_VERSION="04"（生成类似“R11.3_04”的版本号）
  - 默认值 = 无


**"OF_MAINTAINER"**
  - 维护者姓名


**"OF_SCREEN_H"**
  - 如果屏幕纵横比不是16:9，使用此变量
  - 若屏幕宽度不是1080，计算OF_SCREEN_H的公式为：<纵横比高度>*120
  - 例如：如果纵横比是19:9，则使用19*120（=2280）
  - 使用“export OF_SCREEN_H=2280”可将恢复界面调整为19:9屏幕
  - 默认值 = 1920


**"OF_STATUS_H"**
  - 仅当设备有刘海时使用此变量
  - 无需从OF_SCREEN_H中添加或减去OF_STATUS_H
  - 使用“export OF_STATUS_H=144”可将状态栏大小改为144
  - 提示：在Android中截图，使用任何图像编辑器（如MSPaint）计算状态栏高度
  - 默认值 = 72


**"OF_STATUS_INDENT_LEFT"** 和 **"OF_STATUS_INDENT_RIGHT"**
  - 当设备有圆角时，使用OF_STATUS_INDENT_LEFT和OF_STATUS_INDENT_RIGHT
  - 这些变量的推荐设置为48（当设备有圆角时）
  - 默认值 = 20


**"OF_HIDE_NOTCH"**
  - 添加设置选项，使状态栏变黑（隐藏刘海）
  - 默认值 = 0


**"OF_CLOCK_POS"**
  - 用于控制有刘海设备的时钟位置选项
  - 0 => 可选择左、中、右时钟位置
  - 1 => 可选择左、右时钟位置
  - 2 => 仅左时钟位置可用
  - 默认值 = 0


**"OF_ALLOW_DISABLE_NAVBAR"**
  - 如果设备没有硬件导航按钮，设置为0
  - 如果OF_ALLOW_DISABLE_NAVBAR设为0，用户无法隐藏屏幕导航栏
  - 默认值 = 1


**"OF_DONT_KEEP_LOG_HISTORY"**
  - 带时间戳的recovery.log副本（.zip格式）将保存在/sdcard/Fox/logs/中
  - 这意味着之前保存的恢复日志不会被覆盖
  - 日志为.zip格式；用户可能需要定期清理
  - 启用此变量可关闭此功能（即lastrecoverylog.log在每次恢复重启时会被覆盖）
  - 默认值 = 0


**"FOX_BUGGED_AOSP_ARB_WORKAROUND"** [渐废]
 - 用于设置旧的UTC构建时间，以解决某些ROM中存在问题的所谓防回滚保护
 - 例如：export FOX_BUGGED_AOSP_ARB_WORKAROUND="1546300800" [2019年1月1日周二 00:00:00 GMT]
 -     export FOX_BUGGED_AOSP_ARB_WORKAROUND="1420041600" [2014年12月31日周三 16:00:00 GMT]
 - 除非有特定需求设置特定日期/时间，否则可视为渐废标志
 - 默认值 = 0（即默认在启动时自动归零）


**"TARGET_DEVICE_ALT"**
 - 如果设备有多个代号，使用此变量，以便OrangeFox zip安装程序支持替代代号而不会报错退出
 - 例如：export TARGET_DEVICE_ALT="kate"（适用于kenzo/kate）
 -     export TARGET_DEVICE_ALT="willow"（适用于ginkgo/willow）
 -     export TARGET_DEVICE_ALT="blue, green, yellow, orange"（适用于多个替代设备）
 - 默认值 = 无


**"FOX_TARGET_DEVICES"**（从“OF_TARGET_DEVICES”重命名）
- 如果设备有多个代号，但ROM和其他zip安装程序从不检查所有代号，因此总是导致“E3004 error 7”问题（如raphael/raphaelin），使用此变量
- 此变量的作用是使OrangeFox临时切换设备名称以防止error 7
- 注意：这是临时解决方法，仅在恢复重启前有效
- 应列出设备的所有有效代号
- 例如：export FOX_TARGET_DEVICES="raphaelin,raphael"
-
- 注意：此变量的用途与TARGET_DEVICE_ALT（仅影响OrangeFox zip安装程序的创建）完全不同。此变量实际上会影响OrangeFox刷入zip时的操作。这也是它被映射到单独构建变量的原因，目的是在实时恢复会话中触发所有相关影响。只有在所有可能场景中经过充分测试后，才应使用此变量进行构建


**"OF_SKIP_ORANGEFOX_PROCESS"** [遗留]
 - 设置为1可跳过dm-verity/强制加密/boot镜像修补
 - 如果启用此变量，“OF_DONT_PATCH_ON_FRESH_INSTALLATION”也会自动启用
 - 默认值 = 0


**"FOX_VANILLA_BUILD"**（从“OF_VANILLA_BUILD”重命名）
 - 设置为1可创建纯版本构建，跳过所有OrangeFox（主要是MIUI相关）补丁
 - 对于A/B设备、非小米设备（以及不支持MIUI的小米设备构建），可能应启用此变量
 - 如果启用，许多其他变量也会自动启用以禁用各种OrangeFox附加功能，包括上述“OF_SKIP_ORANGEFOX_PROCESS”（详见bootable/recovery/orangefox.mk）
 - 在旧的A-only小米设备上，如果启用此变量，OrangeFox很可能被MIUI原生恢复覆盖，因此用户可能需要刷入某种“fcrypt”zip和/或magisk
 - 默认值 = 0


**"OF_SUPPORT_OZIP_DECRYPTION"**
 - 设置为1可启用对Realme oZip解密的支持
 - 除非知道自己在做什么，否则不要使用（见下文）
 - 如果启用，还必须设置“TW_OZIP_DECRYPT_KEY”
 - 默认值 = 0


**"FOX_REMOVE_AAPT"**
 - 设置为1可从构建中移除aapt二进制文件
 - 此二进制文件大小为1.7mb，使用此变量有助于减小恢复镜像大小
 - 默认值 = 0


**"OF_CHECK_OVERWRITE_ATTEMPTS"** [遗留]
 - 设置为1可检查ROM安装程序是否尝试用其他恢复覆盖OrangeFox
 - 如果启用，OrangeFox将给用户40秒倒计时，以便用户重启OrangeFox停止ROM安装（如果需要）
 - 默认值 = 0


**"OF_FBE_METADATA_MOUNT_IGNORE"**
 - 如果在fstab中支持FBE元数据加密，但不希望用户在不使用元数据加密的ROM中被元数据挂载错误刷屏，设置为1
 - 默认值 = 0


**"OF_IGNORE_LOGICAL_MOUNT_ERRORS"** [!!已过时!!]


**"OF_FIX_OTA_UPDATE_MANUAL_FLASH_ERROR"** [遗留]
- 设置为1可尝试解决用户手动刷入基于块的增量OTA zip时的错误情况
- 仍会显示错误，但OrangeFox将重启进入“Ors”模式并尝试应用OTA更新（不保证始终有效）
- 默认值 = 0


**"OF_PATCH_AVB20"**
- 设置为1可尝试修补AVB 2.0，防止OrangeFox被原生恢复替换
- 通过修补boot镜像实现
- 仅对A-only设备有用，否则会被忽略
- 当使用OF_SKIP_ORANGEFOX_PROCESS或FOX_VANILLA_BUILD时也会被忽略
- 默认值 = 0


**"OF_SUPPORT_VBMETA_AVB2_PATCHING"** [新增] [实验性!!]
- 设置为1可支持通过修补vbmeta/vbmeta_system（而非修补boot镜像）禁用AVB2.0（刷入ROM后）
- 这是实验性功能；请仔细测试，谨慎使用！！
- 大多数设备不需要此功能（2021年前的设备肯定不需要）
- 默认值 = 0


**"OF_OTA_BACKUP_STOCK_BOOT_IMAGE"** [遗留]
- 设置为1可在安装ROM时的OTA_BAK过程中备份ROM zip中的boot.img
- 默认值 = 0


**"OF_SUPPORT_ALL_BLOCK_OTA_UPDATES"** [遗留]
- 设置为1可支持具有此功能的自定义ROM上的基于块的增量OTA
- 如果启用，刷入自定义ROM时将运行与MIUI相同的OTA_BAK/OTA_RES过程
- 此设置与OF_DISABLE_MIUI_SPECIFIC_FEATURES/OF_TWRP_COMPATIBILITY_MODE/FOX_VANILLA_BUILD不兼容
- 默认仅支持MIUI ROM中的基于块的增量OTA
- 默认值 = 0


**"OF_NO_MIUI_PATCH_WARNING"** [遗留]
- 设置为1可抑制关于不修补MIUI可能导致MIUI无法启动或用原生恢复覆盖恢复的警告
- 默认值 = 0


**"OF_DISABLE_MIUI_OTA_BY_DEFAULT"**
- 设置为1可默认禁用OTA设置，即用户必须手动启用OTA
- 默认值 = 0


**"OF_QUICK_BACKUP_LIST"**
- 用于指定“快速备份”默认选中的分区列表
- 注意：仅指定在fstab中挂载的分区，否则会产生错误
- 分区之间用分号分隔，列表以分号结束
- 示例：export OF_QUICK_BACKUP_LIST="/data;/storage;/persist;"


**"OF_USE_LOCKSCREEN_BUTTON"**
- 设置为1可用按钮替换“上滑”锁屏界面
- （目前）对720p屏幕特别有用
- 默认值 = 0


**"FOX_REMOVE_ZIP_BINARY"**
- 设置为1可不添加InfoZip zip二进制文件（用于创建zip归档）
- 必须在构建前在shell脚本或命令行中设置
- 此二进制文件将使恢复镜像大小增加约275kb
- 默认值 = 0


**"OF_ADVANCED_SECURITY"**（从“FOX_ADVANCED_SECURITY”重命名）
- 设置为1可启用高级安全功能
- 这将强制ADB和MTP在进入恢复后（即成功输入所有解密/恢复密码后）才启用
- 默认值 = 0


**"FOX_NO_SAMSUNG_SPECIAL"**（从“OF_NO_SAMSUNG_SPECIAL”重命名） [遗留]
- 设置为1可禁用仅与三星设备相关的某些操作，包括：
-   1) 向三星设备的恢复镜像附加“SEANDROIDENFORCE”
-   2) 创建恢复镜像的Odin可刷tar文件
- 所有解密/恢复密码均已成功输入）
- 默认值 = 0


**"OF_DEVICE_WITHOUT_PERSIST"**
- 如果设备没有/persist分区，设置为1
- 默认值 = 0


**"OF_DISABLE_EXTRA_ABOUT_PAGE"**
- 设置为1可禁用“关于”页面下的额外信息显示
- 仅在显示额外信息导致问题时需要设置
- 默认值 = 0


**"OF_NO_SPLASH_CHANGE"**
- 设置为1可禁用更改开机动画图像
- 仅在更改开机动画图像导致问题时需要设置
- 默认值 = 0


**"FOX_DELETE_MAGISK_ADDON"** [实验性!!!]
- 设置为1可移除/禁用magisk插件
- 如果设置，magisk插件zip将从OrangeFox安装程序中移除，且相关菜单将被禁用，因此无法从“Fox插件”菜单安装magisk
- 此功能为实验性——请仔细测试！
- 默认值 = 0


**"OF_REPORT_HARMLESS_MOUNT_ISSUES"**
- 设置为1可保留将无害挂载问题报告为错误的旧行为
- 默认设置是不报告，或仅作为信息报告而非错误
- 默认值 = 0


**"FOX_DELETE_INITD_ADDON"**
- 设置为1可删除initd插件
- 默认值 = 0


**"FOX_INSTALLER_DEBUG_MODE"**
- 设置为1可将OrangeFox zip安装程序置于调试模式
- 在调试模式下，刷入zip时，大量信息将写入install.log文件
- 仅用于开发者测试——发布前必须禁用
- 默认值 = 0


**"FOX_ENABLE_APP_MANAGER"**
- 设置为1可启用OrangeFox应用管理器（默认已禁用）
- 应用管理器有时会出现问题，尤其是在Android 11及更高版本上——如果不介意，可使用此变量启用
- 默认值 = 0


**"FOX_USE_GREP_BINARY"**
- 设置为1可用完整的独立GNU grep替换toybox的“grep”
- 在原始grep导致问题时有用
- 默认值 = 0


**"FOX_REMOVE_BUSYBOX_BINARY"** [已过时]
- 已过时——见下文“FOX_USE_BUSYBOX_BINARY”


**"FOX_USE_BUSYBOX_BINARY"** [新增] [仅ARM64]
- 设置为1可向构建添加独立的/sbin/busybox二进制文件
- 默认值 = 0


**"FOX_JAVA8_PATH"**
- 用于指向已安装的java-8二进制文件
- 例如：export FOX_JAVA8_PATH="/usr/lib/jvm/java-8-openjdk/jre/bin/java"
- 默认值 = "/usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java"


**"OF_SPLASH_MAX_SIZE"**
- 用于指定可安全用于开机动画图像的最大文件大小（以千字节为单位）
- 某些设备上，最大安全大小为65 Kb；其他设备上，图像可很大（如几兆字节）
- 注意：验证设置的最大值不会太大（即自行测试）
- 如果用户选择的开机动画图像太大，很容易导致“系统已损坏”情况
- 此时，用户需要通过fastboot重新刷入恢复镜像
- 例如：“export OF_SPLASH_MAX_SIZE=128”（即最大128 Kb）
- 默认值 = 4096（即最大4mb）


**"FOX_USE_XZ_UTILS"**
- 设置为1可向构建添加XZ Utils（lzma、xz）（二进制文件大小约260kb）
- 默认值 = 0


**"FOX_DYNAMIC_SAMSUNG_FIX"** [遗留]
- 设置为1可提供针对三星exynos动态分区设备中导致某些二进制文件触发开机循环的漏洞的解决方法
- 启用时，某些二进制文件（如bash、nano、aapt等）将从构建中移除或不添加
- 仅在恢复无法在三星动态分区exynos设备上启动时使用
- 默认值 = 0


**"FOX_USE_NANO_EDITOR"**
   - 设置为1可添加OrangeFox预编译的nano编辑器
   - 必须在构建前在shell脚本或命令行中设置
   - 这将使恢复镜像大小增加约400kb
   - 如果不设置此变量且未设置“FOX_EXCLUDE_NANO_EDITOR”，将尝试从源代码编译nano；如果编译失败，可能需要从上游克隆nano源代码
   - 默认值 = 0


**"FOX_EXCLUDE_NANO_EDITOR"**
- 设置为1可从构建中排除所有nano版本
- 默认值 = 0


**"FOX_BUILD_BASH"** [谨慎使用]
- 设置为1可在构建过程中从源代码编译bash；这将替换标准OrangeFox bash二进制文件
- 这可能需要从上游克隆bash源代码（或如果当前版本产生构建错误，更新源代码）
- 谨慎使用；特别是，shell脚本的shebang应使用“/system/bin/sh”而非“/sbin/sh”


**"FOX_USE_SPECIFIC_MAGISK_ZIP"**
- 用于指向替代的Magisk插件安装程序zip（如果默认版本在设备上不能正常工作，如导致开机循环）
-
- 例如：“export FOX_USE_SPECIFIC_MAGISK_ZIP=~/Magisk/Magisk-v24.3.zip”
-
-     这将（在编译时）用指定的版本替换默认的magisk插件
- 维护者有责任确保替换的magisk zip没有损坏，且在设备的所有股票ROM和自定义ROM上都能正常工作
- 在未在设备支持的所有Android版本的股票ROM和自定义ROM上全面测试要使用的版本之前，不要替换默认的Magisk zip安装程序
- 默认值 = 供应商树中当前的版本


**"FOX_VARIANT"**
- 如果为设备构建特定变体（如针对MIUI、FBE或其他特定变体），使用此变量
- 例如：export FOX_VARIANT=MIUI
- 默认值 = default


**"FOX_CUSTOM_BINS_TO_SDCARD"** [实验性开发中]
- 此功能*高度实验性*，应视为开发中。使用非常繁琐，必须*极其谨慎*
- 仅在*绝对必要*时使用。存在太多潜在问题
- 可用于将一些大型二进制文件移至内部存储，而非放在恢复内存磁盘中。这将减小恢复内存磁盘的大小，这些二进制文件将在运行时可用
- 二进制文件在运行时如何提供给恢复取决于此变量的值
- 可能的值：
- 	"1" = 在运行时将文件复制到内存磁盘
-	"2" = 在运行时创建指向内部存储中文件的符号链接；
- 	"3" = 在构建时创建指向内部存储中文件的符号链接；
-
- 额外必要步骤：
-	必须将下一行代码插入设备树的postrecoveryboot.sh中
		[ -f /sbin/from_fox_sd.sh ] && source /sbin/from_fox_sd.sh
-
- 注意：
- * 某些构建变量与此变量不兼容（如FOX_USE_BASH_SHELL；FOX_ASH_IS_BASH；FOX_DYNAMIC_SAMSUNG_FIX；FOX_USE_GREP_BINARY）
- * 使用此构建变量时，*每次构建都必须执行清洁构建*
- * 用户*必须*刷入OrangeFox zip，而非img——否则二进制文件将无法用于恢复
- * 如果用户格式化/data或删除/sdcard/Fox/FoxFiles/文件夹，二进制文件将无法用于恢复；必须重新刷入OrangeFox zip
- * 如果解密失败，二进制文件将无法用于恢复


**"OF_FORCE_PREBUILT_KERNEL"** [新增]
- 设置为1可避免使用预编译内核时出现新的“无内核配置”错误
- 默认值 = 0


**"OF_SKIP_DECRYPTED_ADOPTED_STORAGE"**
- 设置为1可跳过已解密的 adoptable 存储解密（仅在已安装的ROM高于Android 11时生效，可防止某些A12开机循环）
- 默认值 = 0


**"OF_ENABLE_LPTOOLS"**
- 设置为1可向构建添加phhusson的lptools
- 需要同步lptools源代码（只需从常规位置运行“repo sync”，或如果源代码缺失，按照生成的错误消息中的说明操作）
- 默认值 = 0


**"OF_ENABLE_ALL_PARTITION_TOOLS"**
- 设置为1可启用所有分区工具
- 仅与动态分区设备相关（相当于使用TW_ENABLE_ALL_PARTITION_TOOLS）
- 如果使用此变量，还将自动启用“OF_ENABLE_LPTOOLS”
- 默认值 = 0


**"FOX_PATCH_VBMETA_FLAG"**（从“OF_PATCH_VBMETA_FLAG”重命名） [实验性开发中]
- 设置为1可指示magiskboot v24+在修补恢复/boot镜像时始终修补vbmeta头
- 正常情况下无需使用此变量
- *不要*使用此变量，除非确定自己在做什么（且仅在刷入OrangeFox或Magisk后，或更改开机动画图像后出现开机循环时）
- 这是*实验性*功能，应视为开发中。应彻底测试构建，确保一切正常
- 默认值 = 0


**"OF_FIX_DECRYPTION_ON_DATA_MEDIA"** [新增]
- 如果尽管已尽一切努力，设备上的解密仍失败，设置为1。这可能解决问题——也可能完全无法解决
- 大多数设备根本不需要此变量。但某些设备（如violet和某些Realme设备）需要此设置
- 默认值 = 0


**"FOX_VIRTUAL_AB_DEVICE"**（从“OF_VIRTUAL_AB_DEVICE”重命名）
- 设置为1表示设备肯定是原生Android 11+虚拟A/B（“VAB”）设备
- 如果设置，其他一些相关变量将自动启用（如“FOX_AB_DEVICE”“FOX_VANILLA_BUILD”等）
- 默认值 = 0


**"OF_DYNAMIC_FULL_SIZE"** [新增]
- 仅适用于虚拟A/B设备
- 用于指定ROM安装时“super”分区的实际空间（而非使用update_engine计算的可分配空间）
- 仅在刷入股票ROM失败并显示错误“The maximum size of all groups for the target slot ... has exceeded allocatable space for dynamic partitions...”时有用
- 应使用“super”分区的确切大小（以kb为单位）
- 必须正确确定大小（可能通过运行“fastboot getvar partition-size:super”，然后将结果从十六进制转换为十进制）
- *不要*使用此变量，除非绝对确定自己在做什么；确保提供的值正确是你的责任
- 大多数设备根本不需要
-
- 	例如：“export OF_DYNAMIC_FULL_SIZE=9126805504”
-
- 默认值 = 无


**"OF_NO_REFLASH_CURRENT_ORANGEFOX"** [新增]
- 仅适用于虚拟A/B设备
- 如果启用，将自动移除以下功能
   	* “刷入当前OrangeFox”
   	* “刷入ROM后重新刷入OrangeFox”
- 默认值 = 0


**"FOX_VENDOR_BOOT_RECOVERY"**（从“OF_VENDOR_BOOT_RECOVERY”重命名） [新增 - 实验性]
- 设置为1可构建用于vendor_boot-as-recovery（hdr4）设备的恢复（通常，hdr4设备也是虚拟A/B设备）
- *不要*制作vendor_boot-as-recovery构建，除非知道自己在做什么！极有可能出现开机循环和变砖！
- vendor_boot-as-recovery目前存在问题，自定义恢复仍处于开发初期
- 如果启用此变量，将自动移除以下功能
   	* 更改OrangeFox开机动画图像/标志
   	* “刷入当前OrangeFox”
   	* “刷入ROM后重新刷入OrangeFox”
- 创建的zip安装程序还将包含恢复内存磁盘镜像（“vendor_ramdisk_recovery.cpio”）
- 如果启用，刷入构建的恢复的正确方法是将已安装的工作恢复重启至“fastbootd”模式，
- 解压zip，通过运行命令“fastboot flash vendor_boot:recovery vendor_ramdisk_recovery.cpio”刷入恢复内存磁盘镜像
- （或通过运行zip中捆绑的“flash-ramdisk”脚本）
- 不要刷入zip安装程序本身，除非同时拥有ROM的原始boot.img和vendor_boot.img，*且*知道如何从开机循环或变砖中恢复设备
- 这是因为刷入recovery.img的结果*不可预测*——可能正常，也可能变砖，或使ROM无法启动；任何情况都可能发生
- 除非是*技术高超的冒险者*且能*自行*解决任何问题，否则真的应该远离此变量。已警告过你！
- 默认值 = 0


**"OF_NO_ADDITIONAL_MIUI_PROPS_CHECK"** [新增]
- 设置为1可禁用对MIUI ROM的额外检查
- 对非小米设备和没有Android 12+ MIUI的旧小米设备有用
- 可能减少约半秒的启动时间（不多，但每半秒都有意义！）


**"OF_DEFAULT_TIMEZONE"** [新增]
- 用于更改默认时区
- 例如：export OF_DEFAULT_TIMEZONE="CET-1"
- 有责任提供有效的时区（见gui/theme/common/watch.xml中的示例）
- 默认值 = "CET-1;CEST,M3.5.0,M10.5.0"


**"OF_ENABLE_FS_COMPRESSION"** [新增 - 实验性]
- 设置为1可启用f2fs文件系统压缩
- 需要内核支持和适当的fstab标志
- 不要使用——除非知道自己在做什么，且确定将使用的所有ROM都支持fscompression
- 默认值 = 0


**"FOX_INSTALLER_DISABLE_AUTOREBOOT"** [新增]
- 设置为1可防止OrangeFox安装程序在安装OrangeFox后自动重启至恢复
- 不要在发布构建中使用！
- 仅应在发布前测试时使用，且仅在绝对必要时
- 安装程序在适当情况下（如从OrangeFox刷入vAB/AB设备）已不会自动重启，因此通常无需使用此设置
- 默认值 = 0


**"OF_MANUAL_ROOT_VENDOR_ERROR_FIX"** [新增]
- 设置为1可解决“cannot delete non-empty directory: root/vendor”构建错误
- 仅在绝对必要时使用，因为此构建错误表明设备树有问题，确实应该修复
- 这只是解决方法，而非修复
- 默认值 = 0


**"OF_LOOP_DEVICE_ERRORS_TO_LOG"** [新增]
- 设置为1可防止循环设备挂载错误在恢复控制台刷屏（而是仅将错误写入日志文件）
- 这只是解决方法，而非修复；错误表明设备树有问题，确实应该修复
- 默认值 = 0


**"FOX_USE_DATA_RECOVERY_FOR_SETTINGS"** [新增] [实验性]
- 设置为1可使用/data/recovery/Fox/存储，而非/sdcard/Fox/
- 只有一个优点——因为/data/recovery/始终可用，即使解密失败，设置也能保存和使用
- 最大缺点是每次擦除数据分区时，/data/recovery/都会被删除——因此即使不格式化数据，设置也会丢失
- 此功能*高度实验性*！——不要在未在所有可能场景中进行长期深入测试的情况下使用！
- 默认值 = 0


**"OF_DISPLAY_FORMAT_FILESYSTEMS_DEBUG_INFO"** [新增]
- 设置为1可在格式化数据时显示目标分区的调试信息
- 主要用于测试；不要在发布构建中使用
- 默认值 = 0


**"OF_FORCE_USE_RECOVERY_FSTAB"** [新增]（从“OF_LEGACY_PROCESS_FSTAB”重命名）
- 如果联发科设备上的解密之前正常，现在失败，尝试设置为1以绕过ROM fstab处理，仅使用恢复的fstab
- 此变量相当于在BoardConfig.mk或device.mk中设置“TW_SKIP_ADDITIONAL_FSTAB := true”
- 仅在绝对必要时使用
- 默认值 = 0


**"OF_DEFAULT_KEYMASTER_VERSION"** [新增]
- 用于指定用于解密的keymaster服务的默认版本
- 如果无法自动确定keymaster版本，将使用此值
- 如果使用，应确保指定的是设备树提供的正确keymaster版本
- 例如：“export OF_DEFAULT_KEYMASTER_VERSION=4.0”（即默认使用keymaster 4.0版本）
- 此变量相当于在system.prop或device.mk中设置“keymaster_ver”属性的值
- 如果已安装的ROM使用不同的keymaster版本，将使用该版本并覆盖此处指定的值
- 如果不希望指定的值被覆盖，在BoardConfig.mk或device.mk中设置“TW_FORCE_KEYMASTER_VER := true”
- 默认值 = 空


**"OF_NO_KEYMASTER_VER_4X"** [新增]
- 设置为1可禁用在运行时将keymaster 4.0和4.1属性合并为“4.x”
- 仅作为最后的手段使用
- 如果构建系统是最新的（即在2023年10月10日后运行过“repo sync”），则无需使用
- 不要使用此变量，除非恢复构建在OrangeFox图标处卡住（即使如此，在尝试此变量前应先运行“repo sync”更新构建系统）
- 默认值 = 0


**"FOX_SETTINGS_ROOT_DIRECTORY"** [新增] [实验性!!]
- 允许指定恢复设置/主题的自定义目录（即替代默认的“/sdcard/Fox/”）
- 如果使用，应指定OrangeFox用于其设置/主题内容的设备上的目录
- 指定的目录在恢复运行时必须*始终*可用
- OrangeFox将自动在指定目录的末尾附加“/Fox”
-
- 示例：
-
-  "export FOX_SETTINGS_ROOT_DIRECTORY=/sdcard/PRIVATE"
-
-  "export FOX_SETTINGS_ROOT_DIRECTORY=/sdcard1/MyDev"
-
-  "export FOX_SETTINGS_ROOT_DIRECTORY=/persist/OFRP"
-
- 此功能*高度实验性*，可能存在bug；使用时需*非常*谨慎，并进行大量测试
- 对开发阶段的程序员很重要，可避免测试期间频繁重新配置
-
- 默认值 = 无


**"FOX_MISCELLANEOUS_ROOT_DIRECTORY"** [新增] [实验性!!]
- 允许指定恢复插件、备份、日志、截图等（除设置/主题外）的自定义目录（即替代默认的“/sdcard/Fox/”）
- OrangeFox将自动在指定目录的末尾附加“/Fox”
- 指定的分区应在fstab中显示
-
- 示例：
-
-  "export FOX_SETTINGS_ROOT_DIRECTORY=/sdcard/PRIVATE"
-
-  "export FOX_SETTINGS_ROOT_DIRECTORY=/sdcard1/MyDev"
-
-
- 此功能*高度实验性*，可能存在bug；使用时需*非常*谨慎，并进行大量测试
- 对开发阶段的程序员很重要，可避免测试期间频繁重新配置
-
- 默认值 = 无


**"FOX_ALLOW_EARLY_SETTINGS_LOAD"** [新增]
- 允许OrangeFox在gui刚启动时加载并应用其设置和主题
- 根据可用性，恢复将在gui初始化的早期阶段加载主题和设置
- 默认值 = 0


**"FOX_BASH_TO_SYSTEM_BIN"** [新增]
- 设置为1可在构建时将预编译的bash二进制文件安装到/system/bin/，而非标准的/sbin/
- 如果使用，将在/sbin/中创建指向/system/bin/中二进制文件的符号链接
- 默认值 = 0


**"OF_OPTIONS_LIST_NUM"** [新增]
- 可用于覆盖“选项”列表框（刷入zip时）中出现滚动条前的默认项目数
- 不应在16:9屏幕的设备上使用
- 对使用的任何值的结果负责
- 新数字必须在5到8之间，否则使用默认值
- 示例：
-   "export OF_OPTIONS_LIST_NUM=6"
- 默认值 = 4


**"OF_USE_LEGACY_BATTERY_SERVICES"** [新增]
- 如果状态栏中的电池百分比无法正常工作（如始终显示100%），设置为1
- 默认值 = 0


**"OF_UNBIND_SDCARD_F2FS"**
- 设置为1可尝试在数据格式化或修复前解绑仍处于绑定挂载状态的/sdcard
- 仅在格式化数据有问题时需要
- 默认值 = 0


**"OF_WIPE_METADATA_AFTER_DATAFORMAT"** [新增] [实验性!!]
- 设置为1可在格式化数据分区后自动擦除/metadata
- 谨慎使用：仅在设备/ROM有metadata分区且格式化/data不会自动擦除它时使用
- 有责任自行验证是否需要，且如果使用，需确认其工作正常
- 默认值 = 0


**"OF_BIND_MOUNT_SDCARD_ON_FORMAT"** [新增] [高度实验性!!]
- 设置为1可在格式化数据后自动将/sdcard绑定挂载到/data/media/0，以解决MTP问题
- 注意：此类绑定挂载可能破坏加密；因此仅在绝对必要时使用
- 此功能*高度实验性*，可能存在bug；使用时需*极端*谨慎，并进行大量测试，尤其是带锁屏PIN/密码的情况
- 默认值 = 0


**"OF_UNMOUNT_SDCARDS_BEFORE_REBOOT"** [新增]
- 设置为1可尝试在重启前卸载SD卡
- 默认值 = 0


**"FOX_USE_UPDATED_MAGISKBOOT"** [新增]
- 设置为1可使用更新的（2024+）magiskboot二进制文件。非常新的设备可能需要
- 应严格测试，因为更新的magiskboot二进制文件可能导致问题（如更改开机动画屏幕）
- 如果设备需要使用此变量，但更改开机动画屏幕有问题，可能需要用“OF_NO_SPLASH_CHANGE”禁用开机动画屏幕更改
- 默认值 = 0


**"FOX_EXCLUDE_ZIP"** [新增]
- 设置为1可跳过从源代码构建InfoZip zip二进制文件，而使用预编译版本
- 如果使用，“TW_EXCLUDE_ZIP := true”也将自动设置
- 默认值 = 0


**"FOX_COMPRESS_EXECUTABLES"** [新增] [实验性!!]
- 设置为1可压缩（使用upx）大于128kb的可执行二进制文件（默认在/sbin/和/system/bin/中）
- 此功能为实验性。使用时需非常谨慎；对每个构建的所有功能进行全面测试，确保没有问题
- 如果设为“1”，/sbin/和/system/bin/中符合条件的二进制文件将被压缩
- 如果想手动选择要处理的目录，指定目录而非“1”（例如：export FOX_COMPRESS_EXECUTABLES="/sbin /system/bin /vendor/bin"）
- 默认值 = 0


**"FOX_DRASTIC_SIZE_REDUCTION"** [新增] [!!实验性!!]
- 设置为1可触发一些*剧烈*步骤以减小恢复内存磁盘的大小
- 如果启用，几乎所有OrangeFox额外二进制文件都将从恢复中移除，且“FOX_COMPRESS_EXECUTABLES”将自动启用
- 仅在所有其他减小恢复大小的尝试都失败且恢复仍太大无法适用于设备时使用
- 此功能为实验性。使用时需非常谨慎；对每个构建的所有功能进行全面测试，确保没有问题
- 默认值 = 0


**"FOX_EXTREME_SIZE_REDUCTION"** [新增] [!!实验性!!]
- 设置为1可触发一些相当*极端*的步骤以减小恢复内存磁盘的大小
- 仅在所有其他减小恢复大小的尝试都失败且恢复仍太大无法适用于设备时使用
- 如果启用，“FOX_DRASTIC_SIZE_REDUCTION”也将自动启用，其他功能将被禁用或移除
- 此功能为实验性。使用时需极端谨慎；对每个构建的所有功能进行全面测试，确保没有问题
- 如果证明对太多人有问题，此功能可能被立即撤销
- 默认值 = 0


**"OF_FORCE_DATA_FORMAT_F2FS"** [新增]
- 设置为1可在格式化数据时强制选择f2fs；仅在默认从不选择f2fs时需要
- 谨慎使用！不要使用，除非确定设备上所有目标ROM都支持f2fs！
- 默认值 = 0


**"FOX_MOVE_MAGISK_INSTALLER_TO_RAMDISK"** [新增]
- 设置为1可将Magisk安装程序/卸载程序ZIP移至内存磁盘
- 这将使Magisk插件独立于对/sdcard的访问，但会占用更多内存磁盘空间
- 警告：监控内存磁盘中的可用空间，因为此功能可能占用内存磁盘中的所有可用空间
- 在这种情况下，恢复可能无法启动，尽管构建会正常完成
- 默认值 = 0


**"OF_SKIP_PREBUILT_MODULES"** [新增]
- 设置为1可跳过加载内存磁盘中的预编译内核模块（如果存在）
- 默认值 = 0


**"OF_FORCE_CASEFOLDING"** [新增]
- 设置为1可强制casefolding属性为true
- 这些属性负责用所需参数格式化/data
- 这将禁用从/vendor/build.prop自动检测external_storage.projid.enabled、external_storage.casefold.enabled和external_storage.sdcardfs.enabled属性的值，并强制将其值设为1（external_storage.sdcardfs.enabled除外，设为0）
- 对搭载Android 11+/FBEv2的设备有用，这些设备始终使用casefolding，且如果/vendor不可用，可防止错误确定这些值
- 默认值 = 0


**"FOX_USE_FSCK_EROFS_BINARY"** [新增]
   - 设置为1可添加预编译的fsck.erofs二进制文件（/sbin/fsck.erofs）
   - 必须在构建前在shell脚本或命令行中设置
   - 在添加到构建前，彻底测试以确保在设备上正常工作
   - 默认值 = 0


**"FOX_USE_PATCHELF_BINARY"** [新增]
   - 设置为1可添加预编译的patchelf二进制文件（/sbin/patchelf）
   - 必须在构建前在shell脚本或命令行中设置
   - 在添加到构建前，彻底测试以确保在设备上正常工作
   - 默认值 = 0


**"FOX_USE_DMSETUP"** [新增]
   - 设置为1可使用“dmsetup”尝试解决格式化/data分区的问题
   - 如果启用，dmsetup二进制文件将内置到恢复中，并在格式化/data前调用
   - 仅对具有动态分区的设备/ROM有用
   - 默认值 = 0


**"OF_USE_DMCTL"** [新增]（从“FOX_USE_DMCTL”重命名）
   - 设置为1可使用“dmctl”（作为“dmsetup”的替代）尝试解决格式化/data分区的问题
   - 如果启用，dmsetup二进制文件将内置到恢复中，并在格式化/data前调用
   - 仅对具有动态分区的设备/ROM有用
   - 默认值 = 0


# -----------------------------------

https://gitlab.com/OrangeFox/vendor/recovery/-/blob/master/orangefox_build_vars.txt
