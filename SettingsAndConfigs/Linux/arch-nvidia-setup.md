### Dual OS suggestions: E.g. Dual‑Disk Partition Layout Windows + Arch Linux (EndeavourOS)

- Consider a layout that separates Windows and Arch Linux onto different disks, each with its own EFI System Partition (ESP).
- Consider Ventoy USB-pen, since it allows multiple OS to boot from the same pen.
- Use UEFI when creating the USB
- Install Windows first in Drive1
- Install Linux second in Drive2
- Systemd and Dracut are the default in EndeavourOS
- Latest kernel headers (if you run the mainline kernel)

Partition Table

| Disk | OS            | Partition | Size           | Filesystem | Mount Point | FS Label   | Encryption | Flags |
|------|---------------|-----------|----------------|------------|-------------|------------|------------|-------|
| **Disk 1** | Windows 11 | ESP       | 200 MB–1 GB    | FAT32      | —           | WINESP     | No         | boot, esp |
|      |               | MSR       | 16 MB          | —          | —           | —          | No         | msftres |
|      |               | C:        | Rest of disk   | NTFS       | —           | WINOS      | Optional (BitLocker) | msftdata |
| **Disk 2** | Arch Linux | ESP       | 3.1 GB - 6 GB | FAT32      | `/boot` or `/efi` | ARCHESP    | No         | boot |
|      |               | Root `/`  | 30–85 GB       | ext4       | `/`         | ARCHROOT   | Optional (LUKS) | — |
|      |               | Swap (optional) | 2–32 GB | swap    | —           | ARCHSWAP   | Optional (LUKS) | swap |
|      |               | Var `/var` (optional) | 4–20 GB | ext4 | `/var` | ARCHVAR    | Optional (LUKS) | — |
|      |               | Home `/home` | Remaining space | ext4    | `/home`     | ARCHHOME   | Optional (LUKS) | — | 


---
---
---

### GPU NVidia (aka DarthVader)

NOTE: you need to check which GPU-Driver to use now, try to find out [here](https://wiki.archlinux.org/title/NVIDIA) or [here for endeavour OS](https://forum.endeavouros.com/t/nvidia-gpu-users-attention-please-dec-2025/77119).

- In my case, I tested in a 1060 GTX 6GB (Recommended for GTX 1060 (Pascal))

### 1. Requirements

```
sudo pacman -Syu --needed base-devel dkms git linux-headers
yay -S lib32-nvidia-580-utils nvidia-580xx-utils nvidia-580xx-dkms nvidia-580xx-settings
```

### 2. now, and future kernel updates, you need to repeat this --block--
```
sudo dkms autoinstall
dkms status
uname -r
sudo dracut --force --kver $(uname -r)
sudo reinstall-kernels
```

### 3.1 update boot, or reinstall (when corrupted).
```
sudo bootctl update
# sudo bootctl --esp-path=/efi install || true
```

### 3.2 now reboot, and check if it is working
```
reboot
lsmod | grep nvidia
nvidia-smi
```

### 4.1 Ignore the other drivers, to prevent brick
```
echo -e "blacklist nouveau\noptions nouveau modeset=0" | sudo tee /etc/modprobe.d/blacklist-nouveau.conf
sudo dracut --force --kver $(uname -r)
```

### 4.2 copy/backup pacman.conf, and ignore the DarthVader, I mean, the nvidia packages in the IgnorePkg
```
sudo cp /etc/pacman.conf /etc/pacman.conf.bak
sudo nano /etc/pacman.conf
```

```
IgnorePkg = nvidia nvidia-dkms nvidia-utils nvidia-open nvidia-open-dkms nvidia-lts
```

---

### 5. Vulkan Support, tools and OpenGL (Required)

```
sudo pacman -S vulkan-headers vulkan-tools vulkan-icd-loader lib32-vulkan-icd-loader mesa
glxinfo | grep "OpenGL renderer"
vulkaninfo | grep deviceName
vulkaninfo
vkcube
```

---
---
---

### 6. ZRam set up (Optional)

```
sudo pacman -S zram-generator
sudo nano /etc/systemd/zram-generator.conf
```

```
[zram0]
zram-size = ram/2
compression-algorithm = zstd
swap-priority = 100
```

ZRam start up (it will always be on through different sessions)

```
sudo systemctl daemon-reexec
sudo systemctl restart systemd-zram-setup@zram0.service
swapon --show
```
- You can also turn ZRam off for a sessions:
```
sudo swapoff /dev/zram0
swapon --show
```

---
---
---

### Usual Apps

- Blender
```
sudo pacman -S blender
#-- if you are running from a PC with 2 GPUs:
# prime-run blender 
```

---

- VSCodium
```
yay -S vscodium
```

CMake from twxs

CMake Intellisence from KylinldeTeam

Cmake Tools (Fork) from KylinldeTeam

CodeLLDB from Vadimcn

Kylin CLangd  from KylinldeTeam

Structured Text Language Support from Serhioromano

YAML from Red Hat

NOTE: there was one instance that the Kylin addon was not working correctly with CMake. In this circumstances, just configure and build directly from an OS termninal

---
---
---

- (O3DE) Open 3D Engine from source, github

Many required libs are already listed above. Additional build requirements
```
sudo pacman -S cmake git-lfs python python-pip gcc clang clang-tools-extra ninja llvm lld libc++ perf valgrind cppcheck rapidjson sdl2 ccache
```

Libs O3DE needs as dependencies
```
sudo pacman -S libxcb libx11 libxrandr libxinerama libxcursor libxi
sudo pacman -S alsa-lib openssl
sudo pacman -S qt5-base qt5-tools qt5-declarative qt5-svg qt5-wayland
```

Advised, for documentation of the project
```
sudo pacman -S doxygen graphviz
```

Optional this is just a collection of libs, algorithms... in case you need it
```
sudo pacman -S boost 
```

Advised, so the system is updated
```
sudo pacman -Syu
reboot
```

(0) (O3DE -> Engines -> development) fork; clone the engine source code

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
  -S /home/yourUserName/O3DE/Engines/development/o3de \
  -B /home/yourUserName/O3DE/Engines/development/o3de/build/linux \
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
  --build /home/yourUserName/O3DE/Engines/development/o3de/build/linux \
  --target Editor \
  --config profile \
  --verbose
```

(7) scripts/o3de.sh register --this-engine

---

- (O3DE) Creating the project

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

- How to run it (Arch sometimes present strange UI dimensions)

Create a new shortcut and make sure to set some env variables on the Exec

```
[Desktop Entry]
Name=O3DE Dev Editor
GenericName=
Comment=
Exec=env QT_AUTO_SCREEN_SCALE_FACTOR=0 QT_ENABLE_HIGHDPI_SCALING=1 QT_FONT_DPI=96 /home/yourUserName/O3DE/Projects/SystemPrototypes/build/linux/bin/profile/Editor
Type=Application
Icon=application-x-desktop
Terminal=false
```

---

### outros

- Calculator
```
sudo pacman -S qalculate-gtk
```

- social media videos/audio
```
sudo pacman -S jq

git clone https://github.com/Ld-Hagen/fix-opera-linux-ffmpeg-widevine.git
cd fix-opera-linux-ffmpeg-widevine

./install.sh
```

- Print-Screen with rectangle selection

```
sudo pacman -S flameshot
```

- Steam (for playing, fun or testing your demos. Assuming you already have most drivers and vulkan)

Follow the official suggestions available here: [archlinux.org Steam](https://wiki.archlinux.org/title/Steam). Summary:

```
sudo pacman -Syu

sudo nano /etc/pacman.conf (and make sure the following is uncommented)
```

```
[multilib]
Include = /etc/pacman.d/mirrorlist
```

```
sudo pacman -Syu

#-- you already have this:
# sudo pacman -S nvidia nvidia-utils lib32-nvidia-utils
# yay -S lib32-nvidia-580-utils nvidia-580xx-utils nvidia-580xx-dkms nvidia-580xx-settings

#-- You already have this:
#sudo pacman -S vulkan-icd-loader lib32-vulkan-icd-loader

sudo reboot

sudo pacman -S steam
```

---

### Troubleshooting

- if the updates stop working, use the package cleanup configuration and remove the cache then:

```
sudo -Sy
sudo -Syy
# sudo reboot
```

---


