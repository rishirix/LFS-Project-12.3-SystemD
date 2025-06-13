# 🧰 Temporary Tools (Chapter 5)

This stage focuses on building a **temporary toolchain** that resides in `$LFS/tools`. These tools are used during the construction of the final LFS system in Chapter 6, helping ensure a clean and controlled environment free from host system interference.

---

## 🎯 Purpose of Temporary Tools

The host system’s toolchain (like compilers, linkers, and core utilities) can vary greatly across distributions. To avoid inconsistencies or build failures caused by mismatched versions, LFS instructs building a complete set of tools in a temporary directory. These are isolated from the host and serve as the trusted base for building the actual LFS system.

---

## 🛠️ Environment Setup

I followed the LFS 12.3 book exactly, without modifying any environment variables or instructions. Key environment variables like `$LFS`, `$LFS_TGT`, and the adjusted `PATH` were exported as described in the book.

- **LFS mount point:** `/mnt/lfs`
- **Toolchain location:** `$LFS/tools` (symlinked from `/tools`)
- **Cross target:** `x86_64-lfs-linux-gnu`
- **Init system:** `systemd`

---

## 📦 Packages Built

Each package was compiled in a clean build directory (`mkdir build && cd build`) and installed into the temporary tools directory. Key packages included:

- **Binutils (pass 1 & 2)**
- **GCC (pass 1 & 2)**
- **Linux API Headers**
- **Glibc**
- **Libstdc++**
- **Coreutils**
- **Bash**
- Essential utilities: `make`, `sed`, `tar`, `grep`, `gzip`, `xz`, `diffutils`, `findutils`, `gawk`, etc.

The build order strictly followed the sequence in the LFS book (Chapter 5 & 6).

---

## ⚠️ Problem Faced: Bash Compilation

While compiling **Bash**, I encountered a compiler error. This occurred due to using **GCC 15** on my Arch Linux host system, which is more recent than what LFS was tested against. To fix this:

> I switched to using **Linux Mint** inside the VM, which came with **GCC 13** as the host compiler. This resolved the build issues and ensured compatibility for building GCC 14 as part of the LFS toolchain.

---

## 💡 Key Takeaways

- Compiler **version compatibility** is crucial. Using bleeding-edge versions (like GCC 15) on the host can break builds.
- The isolation provided by the temporary toolchain is vital — without it, host system differences can cause subtle and hard-to-debug failures.
- Building each package in a clean `build/` directory avoids configuration cross-contamination.

---

## 📎 Notes

- I did not preserve build logs in this phase.
- All build instructions, flags, and steps were taken directly from the LFS Book 12.3 (systemd edition).

