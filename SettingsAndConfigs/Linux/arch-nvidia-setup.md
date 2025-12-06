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
| **Disk 2** | Arch Linux | ESP       | 2 GB - 6 GB | FAT32      | `/boot` or `/efi` | ARCHESP    | No         | boot |
|      |               | Root `/`  | 30–60 GB       | ext4       | `/`         | ARCHROOT   | Optional (LUKS) | — |
|      |               | Swap      | 2–32 GB (optional) | swap    | —           | ARCHSWAP   | Optional (LUKS) | swap |
|      |               | Var `/var` (optional) | 4–20 GB | ext4 | `/var` | ARCHVAR    | Optional (LUKS) | — |
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

sudo pacman -S linux-headers

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

vulkaninfo

vkcube

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

yay -S o3de-bin (this fails or skips all files on arch)

sudo pacman -S curl unzip zip tar pkgconf

sudo pacman -S base-devel git

sudo pacman -S git cmake python python-pip gcc clang clang-tools-extra ninja git-lfs llvm lld libc++ perf valgrind cppcheck rapidjson sdl2

sudo pacman -S libxcb libx11 libxrandr libxinerama libxcursor libxi

sudo pacman -S mesa glu

sudo pacman -S alsa-lib openssl

sudo pacman -S qt5-base qt5-tools qt5-declarative qt5-svg qt5-wayland
sudo pacman -S qt6-base qt6-tools qt6-declarative qt6-svg qt6-wayland

sudo pacman -S doxygen graphviz

sudo pacman -S gtk3 gtk4 glade

sudo pacman -S vulkan-icd-loader vulkan-tools vulkan-headers

sudo pacman -S extra/qt5-tools extra/vulkan-headers

sudo pacman -S boost (Optional this is just a collection of libs, algorithms... in case you need it.)

Then you should fork; clone the engine source code (O3DE\Engines\development\);

(1) git lfs install

(2) git lfs fetch --all 

(3) git lfs pull (you have to force it through the cmd, cause some IDEs do not do it by default); 

(4) python/get_python.sh

(5) create a copy of the existing CMakePresets.json file so the file is in the same root; rename it to CMakeUserPresets.json and copy past the content.

Check if "cmake.useCMakePresets" is set to "always" or "auto". CTRL+SHIFT+P -> Cmake: Select Configure Preset, and the presets should be there. IF NOT, reboot your PC, delete the CmakeUserPresets.json and try again (It does not always work at first try)

If the IDE addons to configure and build are not working due to updates, delete the build/linux folder and do it manually (cmake updates or addon updates rarely break, but when they do they do not tell why... they just report a CMake crash with segfault).

- Config

```
cmake --preset custom-linux-clang-ninja
```
or

```
cmake \
  -G "Ninja Multi-Config" \
  -S /home/d/O3DE/Engines/development/o3de \
  -B /home/d/O3DE/Engines/development/o3de/build/linux \
  -DLY_3RDPARTY_PATH=$HOME/O3DE/o3de-packages \
  -DCMAKE_EXPORT_COMPILE_COMMANDS=OFF \
  -DCMAKE_UNITY_BUILD=OFF \
  -DPAL_TRAIT_BUILD_TESTS_SUPPORTED=TRUE \
  -DCMAKE_CXX_FLAGS="-Wno-error -Wno-warnings"
```

- Build

```
cmake --build --preset build-editor-profile
```
or
```
cmake \
  --build /home/d/O3DE/Engines/development/o3de/build/linux \
  --target Editor \
  --config profile \
  --verbose
```

(7) scripts/o3de.sh register --this-engine

---

- Creating the project

scripts/o3de.sh create-project --project-path $HOME/O3DE/Projects/NameOfYourProject

```
cmake -G "Ninja Multi-Config" \
  -B build/linux \
  -DLY_3RDPARTY_PATH="$HOME/O3DE/o3de-packages" \
  -DCMAKE_EXPORT_COMPILE_COMMANDS=OFF \
  -DCMAKE_UNITY_BUILD=OFF \
  -DPAL_TRAIT_BUILD_TESTS_SUPPORTED=TRUE
```

```
cmake --build build/linux --config profile --target Editor NameOfYourProject.GameLauncher
```

### outros

- Calculator

sudo pacman -S qalculate-gtk

---

- social media videos/audio

sudo pacman -S jq

git clone https://github.com/Ld-Hagen/fix-opera-linux-ffmpeg-widevine.git
cd fix-opera-linux-ffmpeg-widevine

./install.sh

---

- Print-Screen with rectangle selection

sudo pacman -S flameshot

--- 

- Steam (for playing, fun or testing your demos. Assuming you already have most drivers and vulkan)

Follow the official suggestions available here: [archlinux.org Steam](https://wiki.archlinux.org/title/Steam). Summary:

sudo pacman -Syu

sudo nano /etc/pacman.conf (and make sure the following is uncommented)

```
[multilib]
Include = /etc/pacman.d/mirrorlist
```

sudo pacman -Syu

sudo pacman -S nvidia nvidia-utils lib32-nvidia-utils

sudo pacman -S vulkan-icd-loader lib32-vulkan-icd-loader

sudo reboot

sudo pacman -S steam

---

### Troubleshooting

- if the updates stop working, use the package cleanup configuration and remove the cache then:

sudo -Sy
sudo -Syy

Then restart

---


