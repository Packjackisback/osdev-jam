---
part: "part-1"
batch: 'osdev'
totalParts: 'total number of parts in the jam'
title: 'Operating Systems Development'
description: 'From 0 to an operating system.'
contributor: 'packjackisback'
contributorSlackID: 'your Slack ID'
thumbnail: 'thumbnail image link'
timeEstimate: ''
difficulty: 'Difficult'
keywords: 'a, list, of, keywords separated by comma space'
presentation: 'link to figma slides'
presentationPlay: 'link to figma slides in presentation mode'
presentationPDF: 'link to pdf of slides'
notes: 'link to notes (optional)'
poster: 'link to poster (optional)'
video: 'link to video (optional)'
---

# Operating Systems Development
---

Chances are, if you’ve been around programming for a while, you’ve heard of the insanity of OS-Dev. Few projects are as low-level and complicated as programming an operating system, and yet, few are as important (think about what you are using to read this Jam ;D) and enjoyable.

This jam will break down the complicated and, arguably, difficult task of making an operating system, and allow anyone to break into the massively enjoyable field of osdev!



## Part 0 - Setting up the environment:
This jam will work best on Linux, but you can definitely follow along on macOS, Windows, and even a web browser, albeit with some extra complications.

First, before we start, we need to install a few tools. We'll explain what they do later, when we actually use them :D .

<details>
<summary>Linux Setup</summary>

--- 

If you have a fairly common distro, the necessary commands to install these should be below!

<details>
<summary>Debian / Ubuntu based distros</summary>

```bash
sudo apt update
sudo apt install build-essential nasm qemu-system-x86 xorriso
```

</details>

<details>
<summary>Fedora based distros (Also RHEL, AlmaLinux, Rocky, etc)</summary>

```bash
sudo dnf groupinstall "Development Tools"
sudo dnf install nasm qemu xorriso
```

</details>


<details>
<summary>Arch based distros</summary>

```bash
sudo pacman -Syu base-devel nasm qemu xorriso
```

</details>


<details>
<summary>openSUSE/ SLE</summary>

```bash
sudo zypper install -t pattern devel_basis
sudo zypper install nasm qemu xorriso
```

</details>


<details>
<summary>Gentoo (Not ChromeOS.)</summary>

```bash
emerge --ask @system
emerge --ask sys-devel/nasm sys-apps/xorriso app-emulation/qemu
```

</details>


<details>
<summary>Alpine</summary>

```bash
sudo apk add build-base nasm qemu xorriso
```

</details>

</details>

<details>
<summary>macOS setup</summary>

Quick warning - I am not familiar with macOS at all. You will probably encounter issues, and will have to learn to fix them on your own.

### Step 1: Install Homebrew
Homebrew is the de-facto macOS package manager. If not installed already, navigate to the terminal and run:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

This script will guide you through the installation. To verify installation after, run:

```bash
brew --version
```

### Step 2: Install Dev Tools

```bash
brew install nasm qemu make
xcode-select --install
```

</details>

<details>
<summary>Windows Setup</summary>

Windows requires a little extra setup to run OS development tools, since most of them are native to Linux. There are two main approaches: **WSL2** (recommended) or **MSYS2**.

---

### Option 1: WSL2 (Recommended)

WSL2 gives you a real Linux environment inside Windows, so you can follow the **Linux instructions** almost directly.

1. Install WSL2:
   - Open PowerShell as Administrator and run:
     ```powershell
     wsl --install
     ```
   - Restart your computer if prompted.
2. Choose a Linux distribution from the Microsoft Store (Ubuntu is easiest).  
3. Open your Linux terminal and follow the **Linux setup commands** (Debian/Ubuntu instructions).  
4. Run QEMU either inside WSL2 or on Windows directly:
   - QEMU can run GUI VMs inside WSL2 with Windows 11/WSLg, or you can copy the kernel image to Windows and run it using Windows QEMU.

---

### Option 2: MSYS2 (Alternative)

MSYS2 provides a Unix-like environment for Windows with a package manager. This is more manual than WSL2 but works.

1. Install MSYS2 from [msys2.org](https://www.msys2.org/).  
2. Update the system:
```bash
pacman -Syu
```

3. Install build tools:
```bash
pacman -S mingw-w64-x86_64-gcc mingw-w64-x86_64-binutils mingw-w64-x86_64-nasm make
pacman -S mingw-w64-x86_64-qemu mingw-w64-x86_64-xorriso
```

</details>




<details>
<summary>Web Setup</summary>
OS Development is not fun to use on web. If you absolutely must, though, it is possible, albeit awful to do.

For the actual development and building, we will be using github codespaces. For the most part, following the debian instructions should work, but if not I recommend google/ asking around, because supporting this is really more effort than it is worth. 

You will then have to build your code to an ISO to test, __every single time__. 

To run said ISO, we will be using [copy.sh](https://copy.sh/v86).

To load an ISO, scroll down to the CD-ROM section, click Browse, and select your ISO. Then, click start.

This will in theory work on any device, but testing is of course necessary.


</details>


You should now have a properly setup development environment. Again, if you can, please use linux. This guide is designed with Linux in mind.
