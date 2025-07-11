
![KDE3.png](attachment/KDE3.png)

![KDE4.png](attachment/KDE4.png)

![KDE2.png](attachment/KDE2.png)

![KDE10.png](attachment/KDE10.png)

![KDE5.png](attachment/KDE5.png)

![KDE1.png](attachment/KDE1.png)


## Watch the Setup in Action on Youtube 
[![Watch the video](attachment/KDE3.png)](https://youtu.be/6guuWjKGW8w)


# Table Of Content

- [Setup 42 Fedora KDE Plasma](#setup-42-fedora-kde-plasma)
- [Getting Started](#getting-started)
  - [Download & Verification](#download--verification)
  - [Creating Bootable Media](#creating-bootable-media)
- [Post-Install Setup](#post-install-setup)
  - [Firmware Updates](#firmware-updates)
  - [Network Configuration](#network-configuration)
  - [DNF Package Manager Tweaks](#dnf-package-manager-tweaks)
  - [Initial System Setup](#initial-system-setup)
  - [Adding Repositories](#adding-repositories)
- [Graphics & Hardware](#graphics--hardware)
  - [Kernel & Build Tools](#kernel--build-tools)
  - [AMD Graphics Setup](#amd-graphics-setup)
  - [NVIDIA Graphics Setup](#nvidia-graphics-setup)
    - [Option 1: RPM Fusion (Recommended for most users)](#option-1-rpm-fusion-recommended-for-most-users)
    - [Option 2: Official NVIDIA Repository](#option-2-official-nvidia-repository)
  - [User Permissions](#user-permissions)
  - [Hybrid Graphics & PRIME](#hybrid-graphics--prime)
  - [GPU Application Offload](#gpu-application-offload)
- [Customization & Extras](#customization--extras)
  - [KDE Customization](#kde-customization)
  - [System Utilities & CPU Control](#system-utilities--cpu-control)
  - [Multimedia & System Settings](#multimedia--system-settings)
  - [Plymouth Boot Splash](#plymouth-boot-splash)
  - [Disabling Unwanted Services](#disabling-unwanted-services)
  - [Adding Custom Gestures on Fedora](#adding-custom-gestures-on-fedora)
- [Final Thoughts](#final-thoughts)


##  Setup 42 Fedora KDE Plasma

This guide is mainly written for Linux newcomers, but if you're already experienced with Linux distros, feel free to skim through and pick what you need. I've tried to make this as comprehensive as possible while keeping things beginner-friendly.

 
## Getting Started

### Download & Verification

First things first - let's get Fedora 42 KDE downloaded safely:

**Download Options:**
- Direct download: [Official Fedora KDE Site](https://fedoraproject.org/kde/)
- Torrent (usually faster): [Fedora Torrent Site](https://torrent.fedoraproject.org/)

**Verification (Don't skip this!):**
Look, I know verification seems tedious, but trust me - one corrupted download and you'll be scratching your head for hours wondering why things aren't working right.

![KDE6.png](attachment/KDE6.png)

1. Download the checksum file the same folder as your ISO

2. Import Fedora's GPG key:
   ```bash
   curl -O https://fedoraproject.org/fedora.gpg
   ```
   (You can verify key details [here](https://fedoraproject.org/security))

3. Verify the checksum file:
   ```bash
   gpgv --keyring ./fedora.gpg Fedora-KDE-42-1.1-x86_64-CHECKSUM
   ```

4. Check if your ISO matches:
   ```bash
   sha256sum --ignore-missing -c Fedora-KDE-42-1.1-x86_64-CHECKSUM
   ```

If everything checks out as "OK" or "valid", you're good to go!

### Creating Bootable Media

Grab a USB drive with at least 4GB space and use the [official Fedora Media Writer](https://flathub.org/apps/org.fedoraproject.MediaWriter). It's reliable and handles everything automatically.

---

## Post-Install Setup

### Firmware Updates

> **⚠️ Important Note**
> 
> If your laptop can update firmware without needing Windows, definitely look into this. It's one of those small things that makes your life easier down the road.

**Before updating to new firmware**, check the discussion forums or blogs related to your specific laptop model.  
If other users report that the **firmware update works without issues**, it’s generally safe to proceed.  
**BIOS updates are especially critical**, so exercise extra caution.  
**Make sure to research thoroughly online before installing any firmware updates.**

```
sudo dnf install fwupd
sudo fwupdmgr refresh
fwupdmgr get-devices
fwupdmgr get-updates
sudo fwupdmgr update
```

**Be careful with that last command!** Use the other commands first to see what's available, cross-check with your vendor's official site, and check forums for any reported issues.

### Network Configuration

I set up a static IP for my laptop on my home router using its MAC address. Fedora randomizes MAC addresses by default (good for privacy), but I needed consistency since my Nextcloud server only accepts connections from specific IPs.

Here's how to use your real MAC address:

1. Click on your WiFi network, then **Configure**
2. Go to **Wi-Fi** tab, find **Restrict to device** section
3. Click the dropdown - you'll see your system's hardware MAC address
4. Copy that MAC address
5. Paste it in the **Cloned MAC Address** field
6. Hit **Apply** and reconnect to WiFi

**Custom DNS Setup:**
While you're there, might as well set up custom DNS:

1. Go to **IPv4** and **IPv6** tabs
2. Under **Method**, choose **Only addresses**
3. In the DNS servers section, add your preferred DNS

I use Google's DNS, but test a few options (Google, Cloudflare, your ISP) and see which works best for your location:

```
Google DNS:
8.8.8.8, 8.8.4.4 (IPv4)
2001:4860:4860::8888, 2001:4860:4860::8844 (IPv6)

Cloudflare DNS:
1.1.1.1, 1.0.0.1 (IPv4)
2606:4700:4700::1111, 2606:4700:4700::1001 (IPv6)
```

### DNF Package Manager Tweaks

Let's speed up package downloads:

```bash
sudo nano /etc/dnf/dnf.conf
```

Add these lines under `[main]`:

```
max_parallel_downloads=10
fastestmirror=1
```

Press **Ctrl + X**, then **Y**, then **Enter** to save.

### Initial System Setup

If you didn't set a hostname during installation, do it now:

```bash
sudo hostnamectl set-hostname <your-hostname>
```

**First System Update:**
For your very first update, I actually recommend using the Discover software center. Yeah, it's slower, but it's more stable and handles any hiccups better than the command line for that crucial first update.

You can always use the faster command later:
```bash
sudo dnf upgrade
```

> **Note:** Don't enable third-party repositories yet - wait until after your first update and reboot.

### Adding Repositories

After your first update and reboot, let's add the good stuff:

**Enable Third-Party Repos via GUI:**
Open **Discover Software Center → Settings** and check:
- RPM Fusion for Fedora 42 - NonFree - NVIDIA Driver
- NonFree - Steam

**Command Line Method:**
```bash
sudo dnf config-manager setopt fedora-cisco-openh264.enabled=1

sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm

sudo dnf install rpmfusion-*-appstream-data

flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```

> **Caution:** If you're having graphics driver issues with RPM Fusion, stick to Fedora's repositories for now. You can add Flatpak support separately.

---

## Graphics & Hardware

### Kernel & Build Tools

Install the essentials for building drivers and modules:

```bash
sudo dnf install kernel-headers kernel-devel-matched
sudo dnf install kernel-headers kernel-devel tar bzip2 make automake gcc gcc-c++ pciutils elfutils-libelf-devel libglvnd-opengl libglvnd-glx libglvnd-devel acpid pkgconfig dkms
```

### AMD Graphics Setup

Good news for AMD users - the open-source drivers are excellent and included by default!

**Install hardware acceleration:**
```bash
sudo dnf install mesa-va-drivers mesa-vdpau-drivers libva-utils vdpauinfo
```

**Verify everything's working:**
```bash
vainfo
vdpauinfo
```

**AMD GPU monitoring (like nvidia-smi for AMD):**
```bash
sudo dnf install amdsmi
```

**For full hardware video decoding**, swap to the "freeworld" versions from RPM Fusion:

```bash
sudo dnf swap mesa-va-drivers mesa-va-drivers-freeworld 
sudo dnf swap mesa-vdpau-drivers mesa-vdpau-drivers-freeworld
```

These freeworld packages unlock additional codecs and acceleration features that are crucial for AMD hardware.

**Important:** Don't mix standard and freeworld versions - it'll cause conflicts during updates. If you're having issues with AMD graphics, stick with the standard Fedora repository versions.

Check [RPM Fusion's documentation](https://rpmfusion.org/Howto) for more details.

**Reboot after installing AMD drivers.**

### NVIDIA Graphics Setup

NVIDIA is trickier. You have two main options:

#### Option 1: RPM Fusion (Recommended for most users)

> **⚠️ Pro Tip:** Don't just blindly upgrade drivers. Check versions first - only install if it's actually newer than what you have.

```bash
sudo dnf install akmod-nvidia
```

**Don't reboot yet!** Wait for the drivers to compile:

```bash
modinfo -F version nvidia
```

If you don't see a version number, wait up to 5 minutes and try again. Only reboot once you see the NVIDIA driver version.

#### Option 2: [Official NVIDIA Repository](https://developer.download.nvidia.com/compute/cuda/repos/)

If RPM Fusion drivers give you trouble, try the official repo. As of writing, there's no Fedora 42 repo yet, but Fedora 41's works fine:

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/fedora41/x86_64/cuda-fedora41.repo
sudo dnf config-manager addrepo --from-repofile=./cuda-fedora41.repo
sudo dnf install kmod-nvidia-latest-dkms nvidia-driver nvidia-driver-cuda nvidia-driver-libs nvidia-driver-cuda-libs nvidia-settings --allowerasing
```

Same deal - verify the driver loaded before rebooting:
```bash
modinfo -F version nvidia
```

**After reboot, install hardware acceleration:**
```bash
sudo dnf install nvidia-vaapi-driver libva-utils vdpauinfo xorg-x11-nvidia
```

### User Permissions

Add yourself to the right groups for GPU access:

```bash
sudo usermod -aG render,video $USER
```

Log out and back in (or reboot) for this to take effect.

### Hybrid Graphics & PRIME

Got a laptop with both integrated and discrete graphics? Let's set that up properly.

**BIOS Settings:**
- Disable Secure Boot
- Enable Switchable Graphics

**Check your setup:**
```bash
/sbin/lspci | grep -e VGA
/sbin/lspci | grep -e 3D
```

If your NVIDIA card shows up in the second command, you're in hybrid mode.

**Verify with:**
```bash
switcherooctl
```

You should see something like:
```
Device: 0
  Name:        AMD Cezanne [Radeon Vega Series]
  Default:     yes
  Environment: DRI_PRIME=pci-0000_05_00_0

Device: 1
  Name:        NVIDIA Corporation TU117M [GeForce GTX 1650]
  Default:     no
  Environment: __GLX_VENDOR_LIBRARY_NAME=nvidia __NV_PRIME_RENDER_OFFLOAD=1 __VK_LAYER_NV_optimus=NVIDIA_only
```

That `Default: no` confirms hybrid mode is working.

**Install [EnvyControl](https://github.com/bayasdev/envycontrol) for easy switching:**
```bash
sudo dnf copr enable sunwire/envycontrol
sudo dnf install python3-envycontrol
```

**KDE Widget:**

Add the "[Optimus GPU Switcher](https://github.com/enielrodriguez/optimus-gpu-switcher)" widget to your panel for easy GPU switching.

### GPU Application Offload

**Command line method (NVIDIA):**
```bash
env __GLX_VENDOR_LIBRARY_NAME=nvidia __NV_PRIME_RENDER_OFFLOAD=1 __VK_LAYER_NV_optimus=NVIDIA_only <your_application>

# Or the simpler way:
switcherooctl launch -g 1 <your_application>
```

**KDE GUI method:**
1. Find the app's `.desktop` file in `/usr/share/applications/` or `~/.local/share/applications/`
2. Right-click the application → Properties → Application tab
3. Advanced Options → Check "Run using dedicated graphics card"

This creates a local `.desktop` file in `~/local/share/applications/` that always runs the app on your discrete GPU.

---

## Customization & Extras

### KDE Customization

Time to make KDE look awesome! First, install build dependencies:

```bash
sudo dnf install git cmake extra-cmake-modules gcc-g++ \
kf6-kwindowsystem-devel plasma-workspace-devel libplasma-devel \
qt6-qtbase-private-devel qt6-qtbase-devel kwin-devel \
kf6-knotifications-devel kf6-kio-devel kf6-kcrash-devel \
kf6-ki18n-devel kf6-kguiaddons-devel libepoxy-devel \
kf6-kglobalaccel-devel kf6-kcmutils-devel kf6-kconfigwidgets-devel \
kf6-kdeclarative-devel kdecoration-devel wayland-devel \
libX11-devel libXext-devel qt6-qttools-devel qt6-qtsvg-devel
```

[**Better Blur Effect**](https://github.com/taj-ny/kwin-effects-forceblur):
```bash
git clone https://github.com/taj-ny/kwin-effects-forceblur.git
cd kwin-effects-forceblur
mkdir build && cd build
cmake ../ -DCMAKE_INSTALL_PREFIX=/usr
make -j
sudo make install
```

[**Kvantum Theme Engine**](https://github.com/tsujan/Kvantum/releases):

Method 1 (latest version):
```bash
# Download latest tar.xz from https://github.com/tsujan/Kvantum/releases
# Example for version 1.1.5:
tar -xf Kvantum-1.1.5.tar.xz
cd Kvantum-1.1.5
cd Kvantum
mkdir build
cd build
cmake ..
sudo make install
```

Method 2 (easier but older):
```
sudo dnf install kvantum
```

### System Utilities & CPU Control

[**Auto CPU Frequency Management**](https://github.com/AdnanHodzic/auto-cpufreq):
```bash
git clone https://github.com/AdnanHodzic/auto-cpufreq.git
cd auto-cpufreq
sudo ./auto-cpufreq-installer
sudo auto-cpufreq --install
```

This tool dynamically manages your CPU frequency based on usage - great for laptop battery life! I’ve included a config file — tweak it however you like.

### Multimedia & System Settings

**KDE System Settings modules:**
```bash
sudo dnf install plymouth-kcm kcm_systemd flatpak-kcm sddm-kcm
sudo dnf install cronie cronie-anacron kcron 
```

**Multimedia codecs:**
```bash
sudo dnf group install multimedia
```

**Font rendering tip:** Go to System Settings → Appearance → Fonts, enable Sub-pixel rendering (RGB) and set Hinting to Medium (or adjust to your preference).

### Plymouth Boot Splash

**Speed up boot by disabling a slow service:**
```bash
sudo systemctl disable NetworkManager-wait-online.service
```

**Change boot splash theme:**
```bash
# List available themes
plymouth-set-default-theme --list

# Set your preferred theme
sudo plymouth-set-default-theme -R <theme-name>
sudo dracut -f
```

### Disabling Unwanted Services

Calender Notification and Discover autostart at boot and use additional RAM. To disable them:

1. Find the `.desktop` file in `/etc/xdg/autostart/` or `~/.config/autostart/`
2. Open it with a text editor
3. Add `Hidden=true` after the `[Desktop Entry]` line and comment out the lines related to autostart phase. 
4. Save the file

This prevents the application from autostarting without completely removing it.

---
### Adding Custom Gestures on Fedora
Setting up custom touchpad gestures on Fedora is actually pretty straightforward — thanks to libinput, which Fedora uses by default.

### 🛠️ First, install the necessary tools:

If you're using **Wayland**, run:

```bash
sudo dnf install xdotool wmctrl ydotool
```

If you're on **X11**, then you only need:

```bash
sudo dnf install xdotool wmctrl
```

### 👤 Add yourself to the input group:

```bash
sudo gpasswd -a $USER input
```

### 📦 Install `libinput-gestures`

You can check out the [official GitHub page](https://github.com/bulletmark/libinput-gestures), but here's the quick and easy version:

```bash
git clone https://github.com/bulletmark/libinput-gestures.git
cd libinput-gestures
sudo ./libinput-gestures-setup install
```

Once that's done, **go ahead and reboot your system** — just to make sure everything loads cleanly.

---

### ✨ Now for the fun part — custom gestures!

If you want to see live gesture input for debugging or experimenting, run:

```bash
sudo libinput debug-events
```

To create your own gestures, you’ll mostly be using `xdotool` (and `ydotool` if `xdotool` does not work on Wayland, and that's a big if, as it works using xwayland). You can explore their documentation if you want more complex setups:

- [xdotool reference](https://github.com/jordansissel/xdotool/blob/master/xdotool.pod) or just run `man xdotool`
    
- `man ydotool` for that one too
    

---

### 📸 Let's make a gesture that takes a screenshot

Normally, pressing the **PrtSc** key takes a screenshot. You can do the same through the terminal like this:

```bash
xdotool key Print
```

> ⚠️ On Wayland, a permission popup might appear. Sadly, it won’t remember your choice after a reboot — known issue. But X11 handles this fine and remembers.

Once that works in your terminal, we can turn it into a gesture.

---

### 📝 Create your gesture config file:

```bash
nvim ~/.config/libinput-gestures.conf
```

Add this simple line:

```bash
gesture hold on 3 xdotool key Print
```

This means: **hold three fingers** on the touchpad and it’ll trigger the PrintScreen key.

---

### 🚀 Make it start on login:

```bash
libinput-gestures-setup autostart
libinput-gestures-setup stop desktop autostart start
```

If it says libinput failed to start, just **reboot your system**, then run that command again.

---

### ✅ Time to test!

Place **three fingers** on the touchpad, hold for 2 seconds, then lift. You should see a screenshot happen — just like magic ✨

---

### 🛑 To stop the gestures:

```bash
libinput-gestures-setup stop
```

### ❌ And to stop it from auto-starting:

```bash
libinput-gestures-setup autostop
```

---

Happy tweaking! Make it yours. You can keep adding more gestures — sky’s the limit 🌟

##### Some additional articles that you may also find useful
- [RAM Management](https://medium.com/@manorit2001/linux-less-ram-want-more-a-guide-to-increasing-your-ram-in-linux-5d5efee6ae2a)
- [Paul Sørensen's Blog](https://paulsorensen.io/fedora-kde-plasma-post-installation-guide/)
- [Arch Wiki](https://wiki.archlinux.org/title/Table_of_contents)
- [KDE Wiki](https://userbase.kde.org/An_introduction_to_KDE)
- [Fedora Wiki](https://docs.fedoraproject.org/en-US/docs/)
## Final Thoughts

That's it! You should now have a properly configured Fedora 42 KDE system with working graphics drivers, optimized performance, and all the multimedia codecs you need.

KDE Plasma is hands down the most customizable desktop environment out there. Seriously if you can imagine it, you can probably make it happen. Want your system to look a certain way? Go wild. The only limit is your imagination.
