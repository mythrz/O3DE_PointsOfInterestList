### Dual OS suggestions: E.g. Dual‑Disk Partition Layout (Windows + Arch Linux with systemd‑boot)

- Consider a layout that separates Windows and Arch Linux onto different disks, each with its own EFI System Partition (ESP).
- Consider Ventoy USB-pen, since it allows multiple OS to boot from the same pen.
- Use UEFI when creating the USB
- Install Windows first in Drive1
- Install Linux second in Drive2

Partition Table

| Disk | OS            | Partition | Size           | Filesystem | Mount Point | FS Label   | Encryption | Flags |
|------|---------------|-----------|----------------|------------|-------------|------------|------------|-------|
| **Disk 1** | Windows 11 | ESP       | 200 MB–1 GB    | FAT32      | —           | WINESP     | No         | boot, esp |
|      |               | MSR       | 16 MB          | —          | —           | —          | No         | msftres |
|      |               | C:        | Rest of disk   | NTFS       | —           | WINOS      | Optional (BitLocker) | msftdata |
| **Disk 2** | Arch Linux | ESP       | 512 MB–1 GB    | FAT32      | `/boot` or `/efi` | ARCHESP    | No         | boot |
|      |               | Root `/`  | 30–60 GB       | ext4       | `/`         | ARCHROOT   | Optional (LUKS) | — |
|      |               | Swap      | 2–32 GB (optional) | swap    | —           | ARCHSWAP   | Optional (LUKS) | swap |
|      |               | Var `/var` (optional) | 2–20 GB | ext4 | `/var` | ARCHVAR    | Optional (LUKS) | — |
|      |               | Home `/home` | Remaining space | ext4    | `/home`     | ARCHHOME   | Optional (LUKS) | — | 


---
---
---

### GPU NVidia

- quick fix (avoid if you can)

sudo pacman -S nvidia-inst

or (long term support)

sudo pacman -S linux-lts-headers

sudo pacman -S nvidia-lts nvidia-utils

or (latest)

sudo pacman -S nvidia nvidia-utils

- Optional (cuda tools and integrated GPUs)

sudo dracut --force

sudo pacman -S nvidia-prime

nvidia-smi

sudo pacman -S cuda

---

- Vulkan Support, tools and OpenGL (if needed, but good to know they are there)

glxinfo | grep "OpenGL renderer"
  
vulkaninfo | grep deviceName

sudo pacman -S vulkan-tools

vulkaninfo

sudo pacman -S vulkan-tools vulkan-icd-loader

---
---
---

### ZRam set up

sudo pacman -S zram-generator

sudo nano /etc/systemd/zram-generator.conf

```
[zram0]
zram-size = ram/2
compression-algorithm = zstd
swap-priority = 100
```
---
---
---

### ZRam start up (it will always be on through different sessions)

sudo systemctl daemon-reexec

sudo systemctl restart systemd-zram-setup@zram0.service

swapon --show

- You can also turn ZRam off for a sessions:

sudo swapoff /dev/zram0

swapon --show

---
---
---

### Usual Apps

- Blender

sudo pacman -S blender

prime-run blender

---

- VSCodium

CMake from twxs

CMake Intellisence from KylinldeTeam

Cmake Tools (Fork) from KylinldeTeam

CodeLLDB from Vadimcn

Kylin CLangd  from KylinldeTeam

Structured Text Language Support from Serhioromano

YAML from Red Hat

Settings:

```
{
    "cmake.pinnedCommands": [
        "workbench.action.tasks.configureTaskRunner",
        "workbench.action.tasks.runTask"
    ],
    "cmake.useCMakePresets": "always",
    "cmake.automaticReconfigure": false,
    "cmake.configureSettings": {
        
    },
    "cmake.allowUnsupportedPresetsVersions": true,
    "cmake.options.statusBarVisibility": "visible"
}
```

---
---
---

- O3DE
  
sudo pacman -S git cmake python gcc clang ninja

sudo pacman -S git-lfs

Then you should fork; clone the engine source code (O3DE\Engines\development\); (1) git lfs install (2) git lfs fetch --all (3) git lfs pull (you have to force it through the cmd, cause some IDEs do not do it by default); create a copy of the existing CMakePresets.json file so the file is in the same root; rename it to CMakeUserPresets.json and copy past the content.

Check if "cmake.useCMakePresets" is set to "always" or "auto". CTRL+SHIFT+P -> Cmake: Select Configure Preset, and the presets should be there. IF NOT, reboot your PC, delete the CmakeUserPresets.json and try again (It does not always work at first try)


### outros

- Calculator

sudo pacman -S qalculate-gtk

- social media videos/audio

sudo pacman -S jq

git clone https://github.com/Ld-Hagen/fix-opera-linux-ffmpeg-widevine.git
cd fix-opera-linux-ffmpeg-widevine

./install.sh

Then restart
