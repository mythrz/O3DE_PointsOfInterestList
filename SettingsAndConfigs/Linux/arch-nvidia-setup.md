
### GPU

- NVidia

sudo pacman -S nvidia-inst

or

sudo pacman -S linux-lts-headers

sudo pacman -S nvidia-lts nvidia-utils

or

sudo pacman -S nvidia nvidia-utils

sudo dracut --force

sudo pacman -S nvidia-prime

nvidia-smi

sudo pacman -S cuda

- Vulkan Support

vulkaninfo

sudo pacman -S vulkan-tools vulkan-icd-loader

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

### ZRam start up (it will always be on through different sessions)

sudo systemctl daemon-reexec

sudo systemctl restart systemd-zram-setup@zram0.service

swapon --show

- You can also turn ZRam off for a sessions:

sudo swapoff /dev/zram0

swapon --show

---

### Usual Apps

- Blender

sudo pacman -S blender

prime-run blender

- VSCodium

CMake from twxs

CMake Intellisence from KylinldeTeam

Cmake Tools (Fork) from KylinldeTeam

CodeLLDB from Vadimcn

Kylin CLangd  from KylinldeTeam

Structured Text Language Support from Serhioromano

YAML from Red Hat

- O3DE
  
sudo pacman -S git cmake python gcc clang ninja

sudo pacman -S git-lfs

Then you should fork, clone the engine source code, create a copy of the existing CMakePresets.json file so the file is in the same root, rename it to CMakeUserPresets.json and copy past the content.

Check if "cmake.useCMakePresets" is set to "always" or "auto". CTRL+SHIFT+P -> Cmake: Select Configure Preset, and the presets should be there. IF NOT, reboot your PC, delete the CmakeUserPresets.json and try again (It does not always work at first try)



