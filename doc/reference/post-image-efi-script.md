---
tags: disk image, Reference 
---

# post-image-efi script

## 目的

在 root filesystem image 建置完成後，產生 OTA 更新所需的額外 image。

此外，在執行 genimage 前，
準備 `grubenv` 以供建立 `efi-part.vfat`。

## 職責

- 建立 OTA restore package
- 建立 OTA update package
- 建立 data partition image
- 準備 genimage 所需檔案

## 執行時機

建置 filesystem image 後，
執行 genimage 前。

```mermaid
%%{init: {
  'flowchart': { 
    'nodeSpacing': 40, 
    'rankSpacing': 40,
    'curve': 'linear'
  },
  'themeVariables': {
    'fontSize': '14px',
    'fontFamily': 'monospace'
  }
}}%%
flowchart TD
    %% Define Styles
    classDef Prerequisites fill:#95CACA
    classDef Image fill:#C2C287

    %% Nodes
    string3["建置 filesystem image<br/>rootfs.ext2 和 rootfs.tar"]
    string4["post-image"]
    string5["genimage"]
    
    %% Connection
    
    string3 --> string4 
    string4 --> string5 
```

## 輸入

- `output/images/rootfs.tar`

## 輸出

- `output/images/rootfs_origin.tar`
- `output/images/rootfs_update.tar`
- `output/images/data.ext2`
- `output/images/efi-part/EFI/BOOT/grubenv`
