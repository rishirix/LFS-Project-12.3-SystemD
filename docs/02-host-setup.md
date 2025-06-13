## 🖥️ Host System Setup

The LFS build was performed inside a virtual machine using **QEMU/KVM** on my daily Linux system. Below are the full details of the host setup and virtualization environment.

---

### 💻 Host Machine Specs

- **Model:** Lenovo Legion Y540
- **CPU:** Intel Core i5 9th Gen
- **RAM:** 16 GB DDR4
- **GPU:** NVIDIA GeForce GTX 1650 (GDDR5)

---

### 🧩 Host Operating System

- **Distribution:** Arch Linux
- **Kernel:** Zen Kernel
- **GPU Drivers:** NVIDIA DKMS (Dynamic Kernel Module Support)
- **Compositor:** Hyprland (Wayland)

---

### 🖥️ Virtual Machine Configuration

The LFS system was built inside a **QEMU/KVM virtual machine** created and managed using `virt-manager`.

- **Guest OS (Temporary):** Linux Mint (used only as a base to build LFS)
- **Allocated RAM:** 8 GB
- **vCPUs:** 2 cores / 2 threads
- **Disk Size:** 100 GB (qcow2 format)
- **Firmware:** Initially UEFI, later switched to BIOS/Legacy mode
- **Architecture:** x86_64
- **Networking:** NAT (via default libvirt bridge)

> 💡 Note: I initially configured the VM with UEFI firmware, but switched to BIOS mode during the final installation phase of LFS due to unfamiliarity with UEFI bootloader setup. This decision allowed me to complete the LFS build without delving into BLFS prematurely.

---

### ⚙️ Virtualization Stack on Host

To set up virtualization on Arch, I installed the following packages:

```bash
sudo pacman -S qemu virt-manager virt-viewer dnsmasq vde2 bridge-utils openbsd-netcat
sudo systemctl enable --now libvirtd
sudo usermod -aG libvirt $(whoami)
