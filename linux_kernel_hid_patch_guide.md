# Contributing to the Linux Kernel: A Concrete Example (HID Driver)

This document provides a detailed, step-by-step guide to contributing a small patch to the Linux kernel. The example demonstrates adding a simple log message to the `hid-core.c` driver and submitting the patch to the kernel mailing list.

---

## 1. Clone the Linux Kernel Repository

```bash
git clone https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
cd linux
```

You can also clone `linux-next` if you're testing experimental features.

---

## 2. Set Up Your Environment (Arch Linux Example)

```bash
sudo pacman -S base-devel bc kmod cpio perl git python rust bind-tools libelf libkcapi xmlto docbook-xsl
```

---

## 3. Configure Git Identity

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

---

## 4. Create a Development Branch

```bash
git checkout -b my-hid-feature
```

---

## 5. Identify the Driver Location

Find your driver's source files:

```bash
git grep hid -- '*Makefile'
```

For example:
```text
drivers/hid/Makefile:obj-$(CONFIG_HID) += hid.o
```

Then check the directory:
```bash
ls drivers/hid/
```

---

## 6. Make Your Changes (Example: Add Log in HID Driver)

Edit `drivers/hid/hid-core.c`:

```c
#include <linux/kernel.h>

// Inside hid_connect function or a similar init function
pr_info("[HID] Device successfully connected\n");
```

Locate the appropriate place inside `hid_connect()` or another relevant function to add your logging statement. Be sure to follow coding style guidelines.

---

## 7. Configure the Kernel

```bash
make mrproper
make defconfig
make menuconfig
```

In the menu:

```
Device Drivers  --->
  HID support  ---> 
    <M> HID bus support
```

This ensures the following appears in your `.config`:
```text
CONFIG_HID=m
```
This builds the HID driver as a module for easier testing.

> Update config :
```bash
make olddefconfig
```
---

## 8. Build and Test Only the Module

### a. Build the Module Only
```bash
make drivers/hid/hid.ko -j$(nproc)
```

### b. Load and Test the Module
```bash
sudo modprobe -r hid          # Unload existing module
sudo insmod drivers/hid/hid.ko  # Load modified module
```

### c. Verify the Message
```bash
sudo dmesg | grep HID
```

Look for your log message: `[HID] Device successfully connected`

---

## 9. Run Checkpatch

```bash
./scripts/checkpatch.pl --strict -f drivers/hid/hid-core.c
```

Fix any warnings or errors reported.

---

## 10. Commit Your Change

```bash
git add drivers/hid/hid-core.c
git commit -s -m "HID: core: Add log message when device connects"
```

The `-s` adds the `Signed-off-by` line required by the Linux kernel.

---

## 11. Generate the Patch

```bash
git format-patch -1
```

This creates a file like `0001-HID-core-Add-log-message.patch`.

---

## 12. Identify the Maintainers

```bash
./scripts/get_maintainer.pl drivers/hid/hid-core.c
```

---

## 13. Configure Git Send-Email

```bash
git config --global sendemail.smtpserver smtp.example.com
# Add your SMTP username and password or set up msmtp
```

---

## 14. Send the Patch

```bash
git send-email --to maintainer@example.com --cc linux-input@vger.kernel.org --cc linux-kernel@vger.kernel.org 0001-HID-core-Add-log-message.patch
```

---

## 15. Respond to Feedback

Wait for feedback on the mailing list. If needed, revise your patch:

```bash
git commit --amend
# edit as needed
git format-patch -v2 -1
```

Send the new version:

```bash
git send-email --to maintainer@example.com --cc linux-input@vger.kernel.org --cc linux-kernel@vger.kernel.org 0001-v2-HID-core-Add-log-message.patch
```

---

## 16. Configure as Built-In (Optional)

If you want to compile the driver as built-in:

```bash
make menuconfig
# Set CONFIG_HID=y
```

---

## 17. Build Full Kernel and Install

Recompile the full kernel:

```bash
make -j$(nproc)
sudo make modules_install
sudo make install
```

Update your bootloader (GRUB is commonly used):

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

---

## 18. Boot and Test on New Kernel

Reboot your machine and select the newly installed kernel from the boot menu.

```bash
uname -r  # Confirm the version matches your compiled kernel
```

Then verify the driver and changes:

```bash
dmesg | grep HID
```

You should see your custom log message if the built-in driver was executed.

---

## 19. Clean Up

After your patch is accepted, you can delete your branch:

```bash
git checkout master
git branch -D my-hid-feature
```

---

## Notes

- Always test your changes thoroughly.
- You do **not** need to recompile the whole kernel if your driver is a module.
- You **must** recompile and boot into the new kernel if the driver is built-in.
- Respect kernel coding standards (use `checkpatch.pl`).

---

## References

- [Submitting Patches Documentation](https://www.kernel.org/doc/html/latest/process/submitting-patches.html)
- [Kernel Newbies Guide](https://kernelnewbies.org)

---

Happy hacking!
