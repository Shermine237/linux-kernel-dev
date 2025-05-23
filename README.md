# 🐧 Linux Kernel Development Environment on Arch Linux

This guide walks you through preparing your Arch Linux system for Linux kernel development, including installing dependencies, building the kernel, and testing it in a virtual machine (QEMU).

---

## ✅ Step 1: Install Required Packages

Open your terminal and install the development tools and required libraries:

```bash
sudo pacman -Syu --needed base-devel ncurses flex bison openssl elfutils bc git
```

These packages include:

- `base-devel`: Essential tools like `gcc`, `make`, `binutils`, etc.
- `ncurses`, `flex`, `bison`: Required for building kernel config interfaces.
- `openssl`, `elfutils`, `bc`: Needed for cryptographic and numeric operations.
- `git`: To clone the kernel source.

---

## 📥 Step 2: Clone the Linux Kernel Source

Clone the official Linux kernel repository maintained by Linus Torvalds:

```bash
git clone https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
cd linux
```

**Optional:** If you only need the latest commit and not the full history:

```bash
git clone --depth=1 https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
cd linux
```

---

## ⚙️ Step 3: Configure the Kernel

### Generate the default configuration for your architecture:
This creates a .config file with default options for your architecture.
```bash
make defconfig
```

### (Optional) Customize the configuration via a TUI:

```bash
make menuconfig
```

This will open a terminal-based UI to enable/disable features, modules, and drivers.

---

## 🏗️ Step 4: Build the Kernel

Compile the kernel using all available CPU cores for faster build time:

```bash
make -j$(nproc)
```

> 🕒 This process can take several minutes depending on your hardware.

The compiled kernel image will be located at:  
`arch/x86/boot/bzImage`

---

## 🧪 Step 5: Test the Kernel Using QEMU

### Install QEMU and Virt-Manager:

```bash
sudo pacman -S qemu virt-manager
```

### Boot your compiled kernel in a virtual machine:

```bash
qemu-system-x86_64 -kernel arch/x86/boot/bzImage -append "console=ttyS0" -nographic
```

> ⚠️ This starts the kernel without an OS. For a full VM setup, consider using an initrd and rootfs.

---

## 🧹 Step 6: Clean Up Build Artifacts

To remove build artifacts:

```bash
make clean        # Remove most generated files
make mrproper     # Remove all generated files including .config
```

---

## 🧠 Tips

- Always use a separate branch when modifying the kernel.
- Validate your changes with `scripts/checkpatch.pl`.
- Read and follow the official [kernel coding style](https://www.kernel.org/doc/html/latest/process/coding-style.html).
- Contribute patches via email as described in `Documentation/process/submitting-patches.rst`.

---

Happy hacking! 🐧
