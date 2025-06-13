# 🏗️ Building the Final System (Chapter 6)

This stage marks the transition from the temporary toolchain to building the final LFS system. Inside a chrooted environment, I compiled and installed every component needed to produce a fully functional, standalone Linux system.

---

## 🧭 Chroot Environment Setup

After completing the temporary tools build, I prepared to chroot into the LFS partition. This included:

- Mounting necessary virtual filesystems:
  ```bash
  mount -v --bind /dev $LFS/dev
  mount -v --bind /dev/pts $LFS/dev/pts
  mount -vt proc proc $LFS/proc
  mount -vt sysfs sysfs $LFS/sys
  mount -vt tmpfs tmpfs $LFS/run
```

After preparing the virtual filesystems, I entered the `chroot` environment using the standard command from the book:

```
chroot "$LFS" /usr/bin/env -i \
    HOME=/root TERM="$TERM" PS1='(lfs chroot) \u:\w\$ ' \
    PATH=/usr/bin:/usr/sbin \
    /bin/bash --login
```

This completely isolated my shell from the host system and placed me inside the LFS root, where the final toolchain and system packages were built natively.

Following the LFS 12.3 book exactly, I built all packages in the provided order, one by one. Each package was extracted and built in `/sources`, usually using a fresh build directory when instructed. I did not modify any variables or flags; all instructions were followed as written.

Initially, I ran the full test suites (`make check`) for each package to verify correctness, but found them to be time-consuming due to my system's limited resources. For example, some packages took over 10 minutes with tests but only 2 minutes without. To speed up the process, I skipped most test suites and only verified tools after the full build was completed.

## ⚠️ Issues Encountered

### 🔸 Networking Failure Post-Install

Upon completing the base LFS build and booting into the system, I discovered that the networking did not work. This was due to missing virtual network drivers in the Linux kernel.

To fix this:

- I **recompiled the kernel** from within LFS with additional modules:
    
    - `virtio` drivers (for virtualized hardware)
        
    - `Intel E1000` driver as fallback
        
- I also switched the virtual network adapter in **Virt-Manager** to `e1000`, as the default `virtio-net` was unsupported without drivers.
    

After making these changes and rebooting, the network worked correctly.

## 🖥️ System Configuration

Several critical configuration files were created before rebooting:

- **Filesystem Table (`/etc/fstab`)** — properly set with UUIDs or device paths
    
- **Hostname (`/etc/hostname`)** — defined the system name
    
- **Host Resolution (`/etc/hosts`)** — mapped hostname to `127.0.0.1`
    
- **Keyboard & Console (`/etc/vconsole.conf`)** — set keyboard layout and console font
    

> Note: I did not create a regular user because LFS doesn’t include a sudo program. Operating as `root` was necessary for system access until I add sudo and user management via BLFS later.

---
Initially, I installed GRUB to `/dev/vda`:

```
grub-install /dev/vda --target=i386-pc --force
```

However, I made an error in the initial `grub.cfg` which caused GRUB to drop into rescue mode on first boot. The mistake was specifying an incorrect device/partition path. After identifying the issue, I edited the configuration:

```
sudo vim /boot/grub/grub.cfg
```

## 🚀 First Boot Experience

With GRUB correctly configured and drivers compiled, I rebooted into the LFS system. The boot process proceeded through `systemd`, reaching a clean login prompt.

At this point:

- Core system utilities were functional

- Networking was operational after kernel recompilation and driver changes

- The system was running entirely independently from the host

## 🧠 Reflections

This phase taught me a lot about the intricacies of building a real Linux system from the ground up. Skipping test suites was a trade-off that saved time, but it helped me recognize the importance of post-build verification. The networking issue and GRUB misconfiguration also offered practical problem-solving opportunities.

> I now have a fully bootable and functioning LFS system, which I plan to extend through **Beyond Linux From Scratch (BLFS)** to add more features and tools.