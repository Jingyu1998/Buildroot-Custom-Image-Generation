---
tags: disk image, Explanation 
---

# post-build mechanism

## 問題

Buildroot 已完成 root filesystem 的建置，
但 OTA 系統建置仍需要加入專案專用的檔案。

例如：
- OTA version metadata
- boot verification 腳本
- OTA install 腳本

## 原因

這些檔案並非 Buildroot package 的一部分，
需要在 root filesystem 建立完成後再加入。

## 設計

本專案需要將少量 OTA 專用檔案安裝至 root filesystem。
由於這些客製化內容都屬於 OTA 建置流程，因此統一由 post-build script 完成，而沒有另外維護一份 rootfs overlay。