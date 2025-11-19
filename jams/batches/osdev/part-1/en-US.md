---
part: "part-1"
batch: 'osdev'
totalParts: 'total number of parts in the jam'
title: 'your title'
description: 'a quick description!'
contributor: 'your GitHub username'
contributorSlackID: 'your Slack ID'
thumbnail: 'thumbnail image link'
timeEstimate: ''
difficulty: '(Beginner, Intermediate, Difficult)'
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
This project is designed for completion on Linux, but setup instructions will be provided for Mac, Windows, and even web!
<details>
<summary>Linux Setup</summary>

--- 

We need to install:

A **C/C++ compiler**: We will be using `gcc` here, but you could conceivably use `clang` if desired.


**Basic utilities**: Provided through `binutils` 99% of the time, and usually preinstalled.  
We need a set of low-level tools like:
- `as` (assembler)  
- `ld` (linker)  
- `objcopy` (convert binaries)  
- `objdump` (inspect binaries)

Don't worry if you don't understand what these do yet, we will learn! These are needed for linking and creating kernel images.


**Make**: A build automation tool that reads a Makefile and compiles what's necessary. 

**Nasm**: Assembled for x86. This will allow us to use assembly, when we start from scratch :D.

**Qemu**: Rebooting and running potentially buggy code repeatedly is a recipe for disaster, so we will be using `qemu`, an emulator/virtual machine.

**xorriso**: This will be used much later, in order to package our code into an iso and run on a machine!

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

MacOS doesn't tend to play nice for developing (Thanks Apple!), and this is especially true with osdev. This will be more difficult than on linux, which this guide is primarily created for.

Most of the tools we need are not installed by default on macOS, but can be added easily with **Homebrew** or Xcode Command Line Tools.

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

The first line is self explanatory, and installs these utilities, but the second line uses xcode to install clang and headers. While most examples will be using gcc, clang should be fully compatible for the scope of the project. 

</details>


