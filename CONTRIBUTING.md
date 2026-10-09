# ❄️ Contributing to Cryo Kernel

First off, thank you for considering contributing to **Cryo Kernel**! It's people like you that make the Android custom kernel community great. 

This document provides guidelines and workflows for contributing to this project. Please read it carefully to ensure a smooth collaboration.

## 📑 Table of Contents
1. [Code of Conduct](#-code-of-conduct)
2. [How Can I Contribute?](#-how-can-i-contribute)
    * [Reporting Bugs](#reporting-bugs)
    * [Suggesting Enhancements](#suggesting-enhancements)
    * [Submitting Pull Requests](#submitting-pull-requests)
3. [Development & Build Setup](#-development--build-setup)
4. [Commit Message Guidelines](#-commit-message-guidelines)

---

## 🤝 Code of Conduct
By participating in this project, you are expected to uphold a welcoming and respectful environment. Harassment, toxic behavior, or disrespect towards other developers or users will not be tolerated. We are all here to learn and build something awesome.

---

## 🛠️ How Can I Contribute?

### Reporting Bugs
If you encounter a bootloop, kernel panic, or a broken feature (e.g., WiFi/Audio not working), please open an Issue. To help us fix it quickly, your report **must** include:
* **Device Variant:** (Topaz or Tapas).
* **Current ROM:** (e.g., HyperOS, Evolution X, LineageOS) and Android Version.
* **Kernel Version:** (e.g., GKI or CLO variant).
* **Logs:** We cannot fix what we cannot see. Please attach:
  * `dmesg` (for kernel logs).
  * `logcat` (for Android framework crashes).
  * `/sys/fs/pstore/` or `/data/tombstones/` (if you experience random reboots or kernel panics).
* **Steps to Reproduce:** How exactly did the issue happen?

### Suggesting Enhancements
Have an idea for a new CPU governor, a network optimization, or a security patch? We'd love to hear it!
* Open an Issue and tag it with `[FEATURE]`.
* Explain **why** this feature is needed and **how** it improves the kernel (performance, battery life, or security).
* If you have a reference patch or commit from another repository, please link it.

### Submitting Pull Requests
We gladly accept Pull Requests (PRs) for bug fixes, upstream patches, and new features.
1. **Fork** the repository.
2. **Create a branch** for your feature or bug fix (`git checkout -b feature/awesome-addition` or `git checkout -b fix/wifi-driver`).
3. **Make your changes** and test them thoroughly on your actual device. *Do not submit untested code.*
4. **Commit** your changes following our [Commit Message Guidelines](#-commit-message-guidelines).
5. **Push** to your fork and submit a Pull Request against the `main` or specific development branch.

---

## 💻 Development & Build Setup
If you want to compile Cryo Kernel locally to test your contributions, ensure you have a Linux environment (Ubuntu 22.04+ recommended) with at least 16GB of RAM/Swap.

**Required Packages:**
```bash
sudo apt-get update && sudo apt-get install -y git ccache automake \
flex lzop bison gperf build-essential zip curl zlib1g-dev \
g++-multilib libxml2-utils bzip2 libbz2-dev libbz2-1.0 \
libghc-bzlib-dev squashfs-tools pngcrush schedtool dpkg-dev \
liblz4-tool make optipng maven libssl-dev pwgen libgl1-mesa-glx \
libgl1-mesa-dev default-jdk python3 python3-pip
```

**Toolchain:**
We strictly use **AOSP Clang** (e.g., `clang-r614150`) for compilation, paired with LTO configurations. Please refer to our `.github/workflows` to see the exact build flags and compiler paths used in production.

---

## 📝 Commit Message Guidelines
Since this is a Linux Kernel project, we strictly follow the standard Linux commit format. A good commit message helps track down bugs (e.g., during `git bisect`).

**Format:**
```text
<subsystem/directory>: <Short, imperative summary of the change>

<Detailed explanation of why the change was made, what it fixes, 
and any relevant context. Wrap text at 72 characters.>

Signed-off-by: Your Name <your.email@example.com>
```

**Examples:**
* ✅ `defconfig: Enable CONFIG_TCP_CONG_BBR`
* ✅ `sched/fair: Inject gaming task isolation`
* ❌ `Added bbr` *(Too vague, no subsystem mentioned)*
* ❌ `Fixed the thing` *(Unhelpful)*

*Note: Always sign-off your commits using `git commit -s`.*

---
*Thank you for helping make Cryo Kernel better! ❄️*
