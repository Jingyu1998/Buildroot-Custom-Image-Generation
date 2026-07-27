---
tags: disk image, Reference 
---

# Buildroot Custom Image Generation Pipeline

## 目的

在 Buildroot 既有的 disk image 建置流程中，加入專案所需要的客製化處理。

本專案的客製化處理主要透過三個機制
* post-build
* post-image
* genimage

## 先決條件

Buildroot 已完成 root filesystem 的建置。

root filesystem 建置完成代表 Buildroot 已將 root filesystem 所需檔案安裝至 `output/target`。

## 職責

* post-build: 
    * 執行 root filesystem 所需的客製化處理
    * 本專案新增 OTA version metadata、OTA update script、OTA boot check script
* 建置 filesystem image: 
    * `rootfs.ext2` 
    * `rootfs.tar`
* post-image:
    * 執行 image 所需的客製化處理
        * Buildroot 
            * 填入 UUID 到 `output/images` 底下的 `grub.cfg`
            * 填入 UUID 到 `output/images` 底下的 `genimage-efi.cfg`
        * 本專案
            * 新增 `grubenv` 到 `output/images` 底下
            * 以 `rootfs.tar` 產生 `rootfs_origin.tar` 和 `rootfs_update.tar`
            * 建置 `data.ext2` filesystem image 
* genimage: 
    * 建置 `efi-part.vfat` filesystem image
    * 建置 `disk.img`

## 流程圖

```mermaid
%%{init: {
  'flowchart': { 
    'nodeSpacing': 40, 
    'rankSpacing': 60,
    'curve': 'linear'
  },
  'themeVariables': {
    'fontSize': '25px',
    'fontFamily': 'monospace'
  }
}}%%
flowchart TD
    %% Define Styles
    classDef Prerequisites fill:#95CACA
    classDef Image fill:#C2C287

    %% Nodes
    string0["make"]
    string1(["root filesystem 建置完成"]):::Prerequisites
    string2["post-build"]
    string3["建置 filesystem image<br/>rootfs.ext2 和 rootfs.tar"]
    string4["post-image"]
    string5["genimage"]
    string6(["disk.img"]):::Image
    
    %% Connection
    string0 --Buildroot build process--> string1
    
    string1 --> string2 --> string3 --> string4 
    string4 --> string5 --> string6
```
