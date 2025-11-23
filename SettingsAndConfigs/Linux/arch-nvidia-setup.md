### Dual OS suggestions

It is better to have each OS on its own drive/disk. Windows tend to monopolize everything. ESP (EFI System Partition) can be shared if you want a united-experience when booting, but there is a small risk windows might mess up the Linux boot (rare, but might happen with larger windows updates). If you separate it, you need to change the starting disk on BIOS whenever you want to change OS.

Install everything with UEFI, Windows 11 has some requirements during installation. Might require Secure Boot too, but once you install Windows, turn it off so you can install Linux.

- Install Windows first in Drive1

Use UEFI when creating the USB. ESP partition during installation is very small (200 MB), which is not enough for multiple OSs if shared. Increase its size for at least 1GB since it has bootloader, kernel images, initramfs files.

Everything else is straight forward.

- Install Linux second in Drive2

Use UEFI when creating the USB. You can use the same ESP, if you increase its size, else use another one.

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

- Vulkan Support

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
