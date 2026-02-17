### Dual OS suggestions: E.g. Dual‑Disk Partition Layout Windows + Arch Linux (EndeavourOS)

- Consider a layout that separates Windows and Arch Linux onto different disks, each with its own EFI System Partition (ESP).
- Consider Ventoy USB-pen, since it allows multiple OS to boot from the same pen.
- Use UEFI when creating the USB
- Install Windows first in Drive1
- Install Linux second in Drive2
- Systemd and Dracut are the default in EndeavourOS
- Latest kernel headers (if you run the mainline kernel)
- WARNING: Dual OS often end up with Windows canabalizing other OS boot-system. If possible, work with EOS only

Partition Table

| Disk | OS            | Partition | Size           | Filesystem | Mount Point | FS Label   | Encryption | Flags |
|------|---------------|-----------|----------------|------------|-------------|------------|------------|-------|
| **Disk 1** | Windows 11 | ESP       | 200 MB–1 GB    | FAT32      | —           | WINESP     | No         | boot, esp |
|      |               | MSR       | 16 MB          | —          | —           | —          | No         | msftres |
|      |               | C:        | Rest of disk   | NTFS       | —           | WINOS      | Optional (BitLocker) | msftdata |
| **Disk 2** | Arch Linux | ESP       | 3.1 GB - 6 GB | FAT32      | `/boot` or `/efi` (advised) | ARCHESP    | No         | boot |
|      |               | Root `/`  | 30–85 GB       | ext4       | `/`         | ARCHROOT   | Optional (LUKS) | — |
|      |               | LinuxSwap or Swap (optional) | 2–32 GB | swap    | —           | ARCHSWAP   | Optional (LUKS) | swap |
|      |               | Var `/var` (optional) | 4–20 GB | ext4 | `/var` | ARCHVAR    | Optional (LUKS) | — |
|      |               | Home `/home` | Remaining space | ext4    | `/home`     | ARCHHOME   | Optional (LUKS) | — | 


---
---
---

### GPU NVidia (aka DarthVader)

NOTE: you need to check which GPU-Driver to use now, try to find out [here](https://wiki.archlinux.org/title/NVIDIA) or [here for endeavour OS](https://forum.endeavouros.com/t/nvidia-gpu-users-attention-please-dec-2025/77119).

- Tested with in two different machines with NVIDIA cards. Desktop 1060 GTX 6GB (Recommended for GTX 1060 (Pascal); and a laptop with GP108M GeForce MX150.

### 1. Requirements

For Desktop:
```
sudo pacman -Syu --needed base-devel dkms git linux-headers
yay -S lib32-nvidia-580xx-utils nvidia-580xx-utils nvidia-580xx-dkms nvidia-580xx-settings
```
For Laptop you need to include this one, so you can choose which GPU to use:
```
yay -S nvidia-prime # DO NOT DO THIS IN DESKTOP
```

Tested two options to mitigate bricks. Option A (automated with no manual hacking) and Option B (manual auto install dkms and force ignore other drivers)


### Option A (advised)

### A.2. EOS comes with dracut by default to manage boot images.

```
sudo reinstall-kernels # or sudo dracut-rebuild
```

### A.3 DRM Modesetting Enabled sometimes helps Vulkan apps perform better (this is optional, you can try it later)

```
sudo nano /etc/kernel/cmdline # nvidia-drm.modeset=1 
```
Add nvidia-drm.modeset=1 to the end of the line. Then Save, reinstall again.

```
sudo reinstall-kernels
reboot
```

Check if the drivers are working correctly
```
lsmod | grep nvidia
nvidia-smi
glxinfo | grep "OpenGL renderer"
```

---

### Option B (worth exploring if A does not work)

### B:2. now, and future kernel updates, you need to repeat this --block--
```
sudo dkms autoinstall
dkms status
uname -r
sudo dracut --force --kver $(uname -r)
sudo reinstall-kernels
```

### B:3.1 update boot, or reinstall (when corrupted)
```
sudo bootctl update
# sudo bootctl --esp-path=/efi install || true
```

### B:3.2 now reboot, and check if it is working
```
reboot
lsmod | grep nvidia
nvidia-smi
```

### B:4.1 Ignore the other drivers, to prevent brick
```
echo -e "blacklist nouveau\noptions nouveau modeset=0" | sudo tee /etc/modprobe.d/blacklist-nouveau.conf
sudo dracut --force --kver $(uname -r)
```

### B:4.2 copy/backup pacman.conf, and ignore the DarthVader, I mean, the nvidia packages in the IgnorePkg
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
```

If you are using an intel processor with integrated graphics, install these two (e.g., Intel UHD 620 can handle low-power Vulkan tasks)
```
sudo pacman -S --needed vulkan-intel lib32-vulkan-intel
```

Check if it is working

```
glxinfo | grep "OpenGL renderer"
vulkaninfo | grep deviceName
vulkaninfo
vkcube
# prime-run vkcube # To test the dedicated GPU on laptops
# prime-run glxinfo | grep "OpenGL renderer"
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
zram-size = ram * 0.6 #zram-size = ram/2 # 50% or 60%
compression-algorithm = zstd
swap-priority = 100
```

ZRam start up (it will always be on through different sessions)

```
sudo systemctl daemon-reexec # sudo systemctl daemon-reload
sudo systemctl restart systemd-zram-setup@zram0.service # sudo systemctl start /dev/zram0
zramctl #swapon --show 
```
- You can also turn ZRam off for a sessions:
```
sudo swapoff /dev/zram0
zramctl #swapon --show
```

---
---
---

### IDE

- VSCodium (You can install O3DE A. Required libs 1st)

```
yay -S vscodium
```

*Extensions:*

CMake from twxs

CMake Intellisence from KylinldeTeam

Cmake Tools (Fork) from KylinldeTeam

CodeLLDB from Vadimcn

Kylin Clangd  from KylinldeTeam

Structured Text Language Support from Serhioromano

YAML from Red Hat

Lua Debugger For O3DE from Galib Arrieta (VSCode only)

NOTE: there was one instance that the Kylin addon was not working correctly with CMake. In this circumstances, just configure and build directly from an OS termninal

---

- Source Control with git: https (recommended)

https is the quickiest and easy way to move on. Go to your provider, generate a new token and clone your repository.

It often asks for a user + password. Note that this is your generated token user + password (NOT your provider credentials)

---

- Source Control with git: SSH (advanced, more secure)

VSCodium/Code integrated terminal often uses a different SSH-agent from the OS terminal. But it is possible to set only one SSH-agent across the terminals.

Create Key (make sure you create it according to your provider. E.g., [GitHub](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) [GitLab](https://docs.gitlab.com/user/ssh/), [IBM](https://cloud.ibm.com/docs/ContinuousDelivery?topic=ContinuousDelivery-git_local), [Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/repos/git/use-ssh-keys-to-authenticate?view=azure-devops), [Atlassian](https://www.atlassian.com/git/tutorials/git-ssh), [AWS](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-ssh-unixes.html), [SourceForge](https://sourceforge.net/p/forge/documentation/SSH%20Keys/))

```
# This is just an example... Make sure you match the needs of your provider. Also, id_rsa or id_ed25519 are usually the default names when generated automatically
ssh-keygen -t rsa -b 4096 -C "email@mail.com userName rng msg" -f ~/.ssh/id_rsa_sha512 -o -a 100
``` 
Display Key to see if it is correct (optional)
```
ssh-keygen -lf ~/.ssh/id_rsa_sha512.pub -E sha512
```
    
Open .ssh/id_rsa_sha512.pub and Copy to your cloud service (e.g., GitHub, GitLab, IBM, Azure, Atlassian, SourceForge, etc...)
    
Edit the .bash_profile file at your home folder (it is a hidden file)
```
#
# ~/.bash_profile
#

# Standard path for the SSH socket. OS Terminal agent = VSCode Terminal agent
export SSH_AUTH_SOCK="$XDG_RUNTIME_DIR/ssh-agent.socket"

[[ -f ~/.bashrc ]] && . ~/.bashrc
```
    
Terminate all running agents, just to be safe (the reboot at the end is technically enough)
```
killall ssh-agent
```
    
Let systemd manage your one and only one SSH agent
```
systemctl --user enable --now ssh-agent
```
    
You can manually add your key, every session;
```
ssh-add ~/.ssh/id_rsa_sha512
```

Or, instead of ssh-add manually, create a ~/.ssh/config. This will automatically add the key, so the step above is not required (do one time, and forget).

```
Host *
  AddKeysToAgent yes
  IdentityFile ~/.ssh/id_rsa_sha512
```
 
If you are changing your existing repo/project to SSH, change the origin
```
git remote set-url origin git@ssh.org.com:/<project>/<repo>
git remote -v
```
    
reboot just to make sure everything is initiating correctly, then test if it is working or make a small commit
```
echo $SSH_AUTH_SOCK # the result should be the same in a OS terminal and VSCodium/Code terminal
ssh-add -l # check if the agent actually has your key loaded
```

From this point forward, never run this! Because it creates another SSH agent, and the objective is to have only one agent across your terminals
```
# eval "$(ssh-agent -s)" # DON'T
```

---
---
---

- (O3DE) Open 3D Engine from source, github

### A. Required libs

Many required libs are already listed above. Additional build requirements
```
sudo pacman -S cmake git-lfs python python-pip gcc clang clang-tools-extra ninja llvm lld libc++ perf valgrind cppcheck rapidjson sdl2 ccache
```

Libs O3DE needs as dependencies (you should already have most if not all of these)
```
# sudo pacman -S libxcb libx11 libxrandr libxinerama libxcursor libxi
# sudo pacman -S alsa-lib openssl
# sudo pacman -S qt5-base qt5-tools qt5-declarative qt5-svg qt5-wayland
sudo pacman -S qt5-tools
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

### B. Cloning, cmake and build

- After rebooting, you can get the source files and build the engine inside the following folders

(Home -> O3DE -> Engines -> development) fork and clone the engine source code

Open VSCodium/Code or a terminal and navigate to your cloned foldere "o3de"

You then MUST use git-lfs and get all other files (REQUIRED), as well as python (REQUIRED)
```
git lfs install
git lfs fetch --all 
git lfs pull

python/get_python.sh
```

create a copy of the existing CMakePresets.json file so the file is in the same root; rename it to CMakeUserPresets.json and copy past the content.

Check if "cmake.useCMakePresets" is set to "always" or "auto". CTRL+SHIFT+P -> Cmake: Select Configure Preset, and the presets should be there, choose Linux Clang Ninja (Custom). IF NOT, reboot your PC, delete the CmakeUserPresets.json and try again (It does not always work at first try).

If the IDE addons to configure and build are not working due to updates, delete the build/linux folder and do it manually (cmake updates or addon updates rarely break, but when they do they do not tell why... they just report a CMake crash with segfault).

After the configure (Linux Clang Ninja (Custom)), you can build (Build Editor Profile (Custom))



### C. If you are using a terminal instead of an IDE to config and build try this, else skip to point D.

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

### D. Register the engine

```
scripts/o3de.sh register --this-engine
```

---

### E.1 (O3DE) Creating a new project

```
scripts/o3de.sh create-project --project-path $HOME/O3DE/Projects/NameOfYourProject
```

Open your VSCodium/Code, open the new project folder, copy the cmakeUserPresets, config and build (similar to what you did for the engine)

If you want to do it in the command line, do this instead, otherwise skip to F.

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

### E.2 (O3DE) Debugging

- Open VSCodium/Code and open the project folder.
- Create a workspace and save the workspace
- Add your custom gems folder to that workspace
- Add your custom gems to your project dependencies with the O3DE menu (avoid building through the menu)
- Create a new folder under your project-folder, called ".vscode", under it, create your launch.json
- Configure with CMake so it generates your compile_commands.json file (either configure VSCodium settings to point to that folder, or just copy-past that file to the root of the project)

This should be the setting that copies the compile_commands.json... But it is not working? Copying it manually worked, and started the index

```
"cmake.copyCompileCommands": "${workspaceFolder}/compile_commands.json"
```

### F. How to run it (Arch sometimes present strange UI dimensions)

Create a new shortcut. 

If you are having troubles with the UI consider setting some env variables on the Exec=env QT_AUTO_SCREEN_SCALE_FACTOR=0 QT_ENABLE_HIGHDPI_SCALING=1 QT_FONT_DPI=96 ...

or set [nvidia-drm.modeset=1](https://github.com/mythrz/O3DE_PointsOfInterestList/blob/main/SettingsAndConfigs/Linux/arch-nvidia-setup.md#a3-drm-modesetting-enabled-sometimes-helps-vulkan-apps-perform-better-this-is-optional-you-can-try-it-later) as mentioned above on A3, and you no longer require the "env" variables.

```
[Desktop Entry]
Name=O3DE Dev Editor
GenericName=
Comment=
Exec=env QT_AUTO_SCREEN_SCALE_FACTOR=0 QT_ENABLE_HIGHDPI_SCALING=1 QT_FONT_DPI=96 /home/yourUserName/O3DE/Projects/NameOfYourProject/build/linux/bin/profile/Editor
Type=Application
Icon=application-x-desktop
Terminal=false
```

---

### outros

- Blender
```
sudo pacman -S blender
#-- if you are running from a PC with 2 GPUs:
# prime-run blender 
```

- FreeCAD
```
sudo pacman -S freecad
```

- Krita
```
sudo pacman -S krita
```

---

- Calculator
```
sudo pacman -S qalculate-gtk
```

---

- social media videos/audio
```
sudo pacman -S vlc-plugins-all
sudo pacman -Syu ffmpeg vlc gst-libav gst-plugins-good gst-plugins-bad gst-plugins-ugly
```

---

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

- LaTeX (full to avoid missing of packages)

```
sudo pacman -S texlive-meta
```

LaTeX Workshop by Jame-Yu

open the TeX tab, build. In the output tab, choose LaTeX compiler to see the outcome of the build

---

- Bluetooth (this comes by default in EOS)

Start
```
sudo systemctl start bluetooth
```

Start at boot
```
sudo systemctl enable bluetooth
```

Enter bluetooth interactive mode (console)
```
bluetoothctl

    power on
    agent on
    default-agent
    scan on
```

Place your device (mouse, headset, joystick, etc...) into pairing mode and locate the device ID (e.g., XX:XX:XX:XX:XX:XX aliasName) then pair, trust for auto-attempt to connect, and connect

```
pair XX:XX:XX:XX:XX:XX
trust XX:XX:XX:XX:XX:XX
connect XX:XX:XX:XX:XX:XX
```
---

- Fire Wall

```
# install
sudo pacman -S ufw

#
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow specific port (when you need it)
sudo ufw allow 8080

# Allow Secure Shell (SSH when you need it)
sudo ufw allow ssh

# start firewall
sudo systemctl enable --now ufw # on boot
sudo ufw enable # enable manually
sudo ufw status verbose # show current status


```

- Anti virus
```
# install
sudo pacman -S clamav 

# Stop, update database, restart
sudo systemctl stop clamav-freshclam
sudo freshclam
sudo systemctl start clamav-freshclam

# scan a folder
clamscan -r -i ~/Downloads

# quarentine
clamscan -r --move=~/quarentine ~/Downloads/Folder1

# instant remove
clamscan -r --remove ~/Downloads/Folder1
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


