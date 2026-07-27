---
tags: disk image, Explanation 
---

# post-image mechanism

## 問題

Buildroot 已完成 root filesystem image ( `rootfs.tar` 、`rootfs.ext2` ) 的建置，但 OTA 系統建置仍需要產生專案專用的 image 與相關檔案。

例如：
- `grubenv`
- 以 `rootfs.tar` 產生 `rootfs_origin.tar` 和 `rootfs_update.tar`
- 建置 `data.ext2` filesystem image 用來存放 `rootfs_origin.tar` 和 `rootfs_update.tar`

## 原因

部分檔案需要依據 Buildroot 產生的 root filesystem image 建立，
因此必須在 image 建立完成後才能產生。

另外，`grubenv` 雖然不依賴 root filesystem image，
但需要在 `genimage` 建立 `efi-part.vfat` 前完成，
才能一併打包至最終 disk image。

## 設計

本專案需要將 root filesystem image 進行後續加工，
因此利用 post-image script 完成 image 相關檔案的建置。

同時，將 genimage 所需的專案檔案集中於 post-image，
使 image 的客製化建置流程皆由同一支 script 管理。