---
tags: disk image, Explanation 
---

# genimage mechanism

## 問題

Buildroot 使用 genimage 生成最終的 disk image。
然而，Buildroot 預設的 partition 配置
並不符合本專案 OTA 系統的需求，
因此需要客製化 disk image。

## 原因

本專案採用 A/B OTA 機制，
需要將 disk image 劃分為不同用途的 partition

例如：
* boot
* rootfsA
* rootfsB
* data

使用 `genimage` 工具可以將不同的 filesystem image 配置到對應的 partition。

| filesystem image | partition |
| ---------------- | --------- |
| `efi-part.vfat`  | boot      |
| `rootfs.ext2`    | rootfsA   |
| `rootfs.ext2`    | rootfsB   |
| `data.ext2`      | data      |

## 設計

Buildroot 使用 genimage 讀取 `genimage.cfg`，並將對應的 filesystem image 組裝成最終的 disk image。

因此本專案修改 `genimage.cfg`，以符合 OTA 系統需求的 partition 配置。

## partition layout in genimage config

```text
disk.img
├── boot
│   └── efi-part.vfat
├── rootfsA
│   └── rootfs.ext2
├── rootfsB
│   └── rootfs.ext2
└── data
    └── data.ext2
```