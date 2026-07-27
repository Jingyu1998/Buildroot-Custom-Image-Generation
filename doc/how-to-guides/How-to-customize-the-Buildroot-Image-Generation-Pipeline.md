---
tags: disk image, How-to Guide
---
# How to customize the Buildroot Image Generation Pipeline

## Prerequisites

* Buildroot 可以使用 `pc_x86_64_efi` 設定檔，成功建置 `disk.img`
* 使用下列指令解壓縮 Repository 提供的 `OTA-material.tar.gz`，得到 `board/pc` 目錄。

```bash
tar -xzvpf OTA-material.tar.gz # -p 用於保留封裝時的檔案權限。
```

* `board/pc` 目錄提供下列檔案
    * `post-build.sh`
    * `post-image-efi.sh`
    * `genimage-efi.cfg`
    * `install.sh`
    * `S99ota-boot-check`

## step 1 確認 buildroot Config

確認 buildroot `.config` 紀錄的
* `post-build.sh` 路徑
* `post-image-efi.sh` 路徑

```bash
grep -R "BR2_ROOTFS_POST_BUILD_SCRIPT=" ./.config
grep -R "BR2_ROOTFS_POST_IMAGE_SCRIPT=" ./.config
```

以本專案為例, 路徑結果如下

```
BR2_ROOTFS_POST_BUILD_SCRIPT="board/pc/post-build.sh"
BR2_ROOTFS_POST_IMAGE_SCRIPT="board/pc/post-image-efi.sh"
```

## step 2 Copy the OTA project files

將 OTA 專案提供的檔案複製到 step 1 查詢出的目錄。
以本專案為例, 是將下列五個檔案都複製到 `board/pc` 目錄裡

這些檔案包含：

* `post-build.sh`
* `post-image-efi.sh`
* `genimage-efi.cfg`
* `install.sh`
* `S99ota-boot-check`

這些檔案會分別在 Buildroot Image Generation Pipeline 的不同階段被使用：

| File                | Used by    |
| ------------------- | ---------- |
| `post-build.sh`     | post-build |
| `post-image-efi.sh` | post-image |
| `genimage-efi.cfg`  | genimage   |
| `install.sh`        | post-build |
| `S99ota-boot-check` | post-build |

## step 3 Run make

在 buildroot 根目錄執行 `make`

## step 4 Observe the Build Log 

本 Guide 使用專案額外加入的 Build Log 來觀察 Buildroot Image Generation Pipeline。

post-build

```
>>>      Create OTA version metadata in output/target/etc/ota-version
>>>      Install OTA update script in output/target/root/install.sh
>>>      Install OTA boot check script in output/target/etc/init.d/S99ota-boot-check
```

post-image

```
>>>      Set the default vallue of grubenv in output/images/efi-part/EFI/BOOT/grubenv
>>>      Created rootfs_origin.tar and rootfs_update.tar in output/images
>>>      Created the data.ext2 in output/images
>>>      Start support/scripts/genimage.sh
```

genimage 

```
>>>   Executing genimage script support/scripts/genimage.sh
>>>      Start genimage tool to assemble disk image
>>>      Finish genimage tool
>>>      Created the efi-part.vfat in output/images
>>>      Created the disk image in output/images
```

## step 5 Verify the outputs

### post-build

Verify that the following files exist:

* `output/target/etc/ota-version`
* `output/target/root/install.sh`
* `output/target/etc/init.d/S99ota-boot-check`

### post-image

Verify that the following files exist:

* `output/images/rootfs_origin.tar`
* `output/images/rootfs_update.tar`
* `output/images/data.ext2`
* `output/images/efi-part/EFI/BOOT/grubenv`

### genimage

Verify that the following files exist:

* `output/images/efi-part.vfat`
* `output/images/disk.img`