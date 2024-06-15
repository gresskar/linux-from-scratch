# Linux From Scratch: RISC-V

Linux with Musl and BusyBox targeting RISC-V, from scratch!

# 1. Prerequisites

## 1.1. Dependencies

| **Dependency** | **Debian pkg**   | **Fedora pkg**   | **Arch pkg** | **Gentoo pkg**        | **Purpose**                  |
|----------------|------------------|------------------|--------------|-----------------------|------------------------------|
| Git            | `git`            | `git`            | `git`        | `dev-vcs/git`         | Fetches source code          |
| CMake          | `cmake`          | `cmake`          | `cmake`      | `dev-build/cmake`     | Build system                 |
| Ninja          | `ninja-build`    | `ninja-build`    | `ninja`      | `dev-build/ninja`     | Build system                 |
| Make           | `make`           | `make`           | `make`       | `dev-build/make`      | Automates running tasks      |
| Autoconf       | `autoconf`       | `autoconf`       | `autoconf`   | `dev-build/autoconf`  | Automates configuration      |
| Automake       | `automake`       | `automake`       | `automake`   | `dev-build/automake`  | Automates making Makefiles   |
| GCC            | `gcc`            | `gcc`            | `gcc`        | `sys-devel/gcc`       | Compiles C code              |
| G++            | `g++`            | `g++`            | `gcc`        | `sys-devel/gcc`       | Compiles C++ code            |
| Curl           | `curl`           | `curl`           | `curl`       | `net-misc/curl`       | Remote data transfer tool    |
| Python3        | `python3`        | `python3`        | `python3`    | `dev-lang/python `    | Programming language         |
| mpfr           | `libmpfr-dev`    | `mpfr-devel`     | `mpfr`       | `dev-libs/mpfr`       | C library for math stuff     |
| libmpc         | `libmpc-dev`     | `libmpc-devel`   | `libmpc`     | `dev-libs/mpc`        | C++ library for math stuff   |
| gmplib         | `libgmp-dev`     | `gmp-devel`      | `gmp`        | `dev-libs/gmp `       | C/C++ library for math stuff |
| zlib           | `zlib1g-dev`     | `zlib-devel`     | `zlib`       | `sys-libs/zlib`       | Data compression             |
| expat          | `libexpat-dev`   | `expat-devel`    | `expat`      | `dev-libs/expat`      | XML parser                   |
| GLib           | `libglib2.0-dev` | `glib2-devel`    | `glib2`      | `dev-libs/glib`       | Low-level C librarues        |
| Slirp          | `libslirp-dev`   | `libslirp-devel` | `libslirp`   | `net-libs/libslirp`   | Emulates internet connection |
| Bison          | `bison`          | `bison`          | `bison`      | `sys-devel/bison`     | Generates parsers            |
| Texinfo        | `texinfo`        | `texinfo`        | `texinfo`    | `sys-devel/flex`      | Generates documentation      |
| flex           | `flex`           | `flex`           | `flex`       | `sys-apps/texinfo`    | Generates lexical analyzers  |
| gperf          | `gperf`          | `gperf`          | `gperf`      | `dev-util/gperf`      | Generates hash functions     |
| Libtool        | `libtool`        | `libtool`        | `libtool`    | `dev-build/libtool`   | Simplifies using shared libs |
| patchutils     | `patchutils`     | `patchutils`     | `patchutils` | `dev-util/patchutils` | Tools for patching           |
| Gawk           | `gawk`           | `gawk`           | `gawk`       | `sys-apps/gawk`       | Text processing tool         |
| bc             | `bc`             | `bc`             | `bc`         | `sys-devel/bc`        | Basic calculator             |
| DTC            | N/A              | `dtc`            | `dtc`        | `sys-apps/dtc`        | Device tree compiler         |

### 1.1.1. Installing dependencies: Debian Linux

*This should also work with Ubuntu, Mint, etc.*

**WARNING:** Debian doesn't seem to have `dtc` in repo? You need to get that manually.

```Shell
sudo apt update && sudo apt upgrade -y

sudo apt install git cmake ninja-build make autoconf automake gcc g++ curl python3 libmpfr-dev libmpc-dev libgmp-dev zlib1g-dev libexpat-dev libglib2.0-dev libslirp-dev bison texinfo flex gperf libtool patchutils gawk bc
```

### 1.1.2. Installing dependencies: Fedora Linux

*This should also work with RHEL, CentOS, etc.*

```Shell
sudo dnf update && sudo dnf upgrade -y

sudo dnf install git cmake ninja-build make autoconf automake gcc g++ curl python3 mpfr-devel libmpc-devel gmp-devel zlib-devel expat-devel glib2-devel libslirp-devel bison texinfo flex gperf libtool patchutils gawk bc dtc
```

### 1.1.3. Installing dependencies: Arch Linux

*This should also work with Artix, Manjaro, etc.*

```Shell
sudo pacman -Syu git cmake ninja make autoconf automake gcc python3 mpfr libmpc gmp zlib expat glib2 libslirp bison texinfo flex gperf libtool patchutils gawk bc dtc
```

### 1.1.4. Installing dependencies: Gentoo Linux

```Shell
sudo emerge --sync

sudo emerge -av dev-vcs/git dev-build/cmake dev-build/ninja dev-build/make dev-build/autoconf dev-build/automake sys-devel/gcc net-misc/curl dev-lang/python dev-libs/mpfr dev-libs/mpc dev-libs/gmp sys-libs/zlib dev-libs/expat net-libs/libslirp sys-devel/bison sys-devel/flex sys-apps/texinfo dev-util/gperf dev-build/libtool dev-util/patchutils sys-apps/gawk sys-devel/bc sys-apps/dtc
```

## 1.2. Directories

- `/opt/riscv/src/` : All source code will be stored here
- `/opt/riscv/boot/` : U-Boot & Linux will be stored here
- `/opt/riscv/share/qemu/` : OpenSBI firmware will be stored here
- `/opt/riscv/rootfs` : BusyBox will be stored here
- `/opt/riscv/bin/` : GCC, Binutils & QEMU executables will be stored here
- `/opt/riscv/dev/` : Virtual disk image will be stored here

```Shell
sudo mkdir -v -p /opt/riscv/{src,boot,share,rootfs,bin,dev}/

sudo chown -v -R $(id -un):$(id -gn) /opt/riscv/

sudo chmod -v -R 774 /opt/riscv/
```

# 2. QEMU (emulator)

## 2.1 Fetch sourc ecode

```Shell
cd /opt/riscv/src/

git clone https://github.com/qemu/qemu.git qemu
```

## 2.2. Configure QEMU

Omit `--enable-avx2` if `grep 'avx2' /proc/cpuinfo` returns nothing!

```Shell
cd ./qemu/

git checkout tags/v9.0.1 # this is current stable (15/06/2024)

mkdir -v ./build/ ; cd ./build/

../configure --prefix=/opt/riscv/ --target-list=riscv64-linux-user,riscv64-softmmu --without-default-features --enable-avx2
```

- `--prefix=/opt/riscv/` : install to `/opt/riscv/` (*default is `/usr/local/`*)

- `--target-list=riscv64-linux-user,riscv64-softmmu` : build user-mode and system-mode binaries

- `--without-default-features` : disable bloat

- `--enable-avx2` : enable CPU optimizations


## 2.3 Build QEMU

```Shell
make -j$(nproc)
```

## 2.4. Install QEMU

```Shell
make install
```

# 3. Toolchain

## 3.1. Fetch source code

```Shell
cd /opt/riscv/src/

git clone https://github.com/riscv-collab/riscv-gnu-toolchain.git toolchain
```

## 3.2. Configure toolchain

```Shell
cd ./toolchain/

git checkout tags/2024.04.12 # this is current stable (15/06/2024)

mkdir -v ./build/ ; cd ./build/

../configure --prefix=/opt/riscv --disable-debug-info --disable-multilib
```

- `--prefix=/opt/riscv/` : install to `/opt/riscv/` (*default is `/usr/local/`*)

- `--disable-debug-info` : disable debug information - this speeds up compilation and uses less space

- `--disable-multilib` : don't build for 32-bit - only build for 64-bit

**TODO:** I also tried with `--disable-gcc-checking --disable-gdb --disable-llvm --disable-host-gcc --disable-libsanitizer --disable-qemu-system`, but it broke. Need to figure out exactly what flag caused it and why...

## 3.3. Build toolchain

```
make musl -j$(nproc)
```

## 3.4. Test toolchain

Create the file `/tmp/test.c` with the contents:

```C
#include <stdio.h>

int main(void)
{
    printf("Hello world!\n");
    return 0;
}
```

Compile and run it with:

```Shell
PATH="/opt/riscv/bin" riscv64-unknown-linux-musl-gcc -static /tmp/test.c -o /tmp/test

PATH="/opt/riscv/bin" qemu-riscv64 /tmp/test
```

# 4. OpenSBI (BIOS firmware)

## 4.1. Fetch source code

```Shell
cd /opt/riscv/src/

git clone https://github.com/riscv-software-src/opensbi.git opensbi
```

## 4.2. Build OpenSBI

```Shell
cd ./opensbi/

git checkout tags/v1.4 # this is current stable (15/06/2024)

make ARCH="riscv" CROSS_COMPILE="riscv64-unknown-linux-musl-" PATH="/opt/riscv/bin:$PATH" O=./build/ -j$(nproc)
```

## 4.3. Install OpenSBI

```Shell
make I=/opt/riscv/ install
```

# 5. Linux (kernel)

## 5.1. Fetch source code

```Shell
cd /opt/riscv/src/

git clone https://github.com/torvalds/linux.git linux
```

## 5.2. Configure Linux

```Shell
cd ./linux/

git checkout tags/v6.9 # this is current stable (15/06/2024)

make ARCH="riscv" CROSS_COMPILE="riscv64-unknown-linux-musl-" PATH="/opt/riscv/bin:$PATH" distclean

make ARCH="riscv" CROSS_COMPILE="riscv64-unknown-linux-musl-" PATH="/opt/riscv/bin:$PATH" menuconfig
```

Important options:



## 5.3. Build Linux
```Shell
make ARCH="riscv" CROSS_COMPILE="riscv64-unknown-linux-musl-" PATH="/opt/riscv/bin:$PATH" Image -j$(nproc)
```

## 5.4. Install Linux

```Shell
cp -v ./arch/riscv/boot/Image /opt/riscv/boot/linux.efi
```

# 6. U-Boot (boot loader)

## 6.1. Fetch source code

```Shell
cd /opt/riscv/src/

git clone https://github.com/u-boot/u-boot.git u-boot
```

## 6.2. Configure U-Boot

```Shell
cd u-boot

git checkout tags/v2024.04 # this is current stable (15/06/2024)

make ARCH="riscv" CROSS_COMPILE="riscv64-unknown-linux-musl-" PATH="/opt/riscv/bin:$PATH" qemu-riscv64_smode_defconfig

make ARCH="riscv" CROSS_COMPILE="riscv64-unknown-linux-musl-" PATH="/opt/riscv/bin:$PATH" menuconfig 
```

Important options:

- Environment
    - [\*] Environment is in a FAT filesystem
    - (virtio) Name of the block device for the environment (NEW)
    - (0:1) Device and partition for where to store the environemt in FAT

## 6.3. Compile U-Boot

```Shell
make ARCH="riscv" CROSS_COMPILE="riscv64-unknown-linux-musl-" PATH="/opt/riscv/bin:$PATH" u-boot -j$(nproc)
```

## 6.4. Install U-Boot

```Shell
cp -v ./u-boot /opt/riscv/boot/u-boot.elf
```

# 7. BusyBox (user space)

## 7.1. Fetch source code

```Shell
cd /opt/riscv/src/

git clone https://git.busybox.net/busybox
```

## 7.2. Patch BusyBox

Building on Arch seems to be a little broken, so I made a patch file `ncurses.patch`:

```Patch
--- busybox/scripts/kconfig/lxdialog/check-lxdialog.sh  2024-06-15 16:26:13.533285951 +0200
+++ busybox/scripts/kconfig/lxdialog/check-lxdialog-new.sh      2024-06-15 16:19:56.506098840 +0200
@@ -47,7 +47,7 @@
 check() {
         $cc -x c - -o $tmp 2>/dev/null <<'EOF'
 #include CURSES_LOC
-main() {}
+int main() {}
 EOF
        if [ $? != 0 ]; then
            echo " *** Unable to find the ncurses libraries or the"       1>&2
```

```Shell
patch -p0 < ncurses.patch
```

## 7.3. Configure BusyBox

```Shell
cd busybox

git checkout tags/1_36_1 # this is current stable (15/06/2024)

make ARCH="riscv" CROSS_COMPILE="riscv64-unknown-linux-musl-" PATH="/opt/riscv/bin:$PATH" allnoconfig

make ARCH="riscv" CROSS_COMPILE="riscv64-unknown-linux-musl-" PATH="/opt/riscv/bin:$PATH" menuconfig
```

Important options:

**TODO:** trunctuate this or just give the `.config` file

- **Settings**
    - [\*] Build static binary (no shared libs)
    - (riscv64-unknown-linux-musl-) Cross compiler prefix
- **Archival Utilities**
    - [\*] Make tar, rpm, modprobe etc understand .xz data
    - [\*] Make tar, rpm, modprobe etc understand .bz2 data
    - [\*] Make tar, rpm, modprobe etc understand .gz data
    - [\*] gzip
        - [\*]   Enable decompression
    - [\*] tar
        - [\*]   Enable -c (archive creation)
    - [\*] unzip
- **Coreutils**
    - [\*] basename
    - [\*] cat
    - [\*] chgrp
    - [\*] chmod
    - [\*] chown
    - [\*] chroot
    - [\*] cp
    - [\*] cut
    - [\*] date
    - [\*] dd
    - [\*] df
        - [\*] Enable -a, -i, -B 
    - [\*] dirname
    - [\*] du
    - [\*] echo
        - [\*] Enable -n and -e options
    - [\*] env
    - [\*] head
    - [\*] id
    - [\*] groups
    - [\*] install
    - [\*] ln
    - [\*] ls
        - [\*] Show file timestamps
        - [\*] Show username/groupnames
    - [\*] md5sum
    - [\*] sha1sum
    - [\*] sha256sum
    - [\*] sha512sum
    - ---   Common options for md5sum, sha1sum, sha256sum, sha512sum, sha3sum
        - [\*]   Enable -c, -s and -w options
    - [\*] mkdir
    - [\*] mknod
    - [\*] mv
    - [\*] nproc
    - [\*] pwd
    - [\*] readlink
        - [\*] Enable canonicalization by following all symlinks (-f)
    - [\*] rm
    - [\*] shred
    - [\*] sleep
    - [\*] sort
        - [\*] Use less memory (but might be slower)
    - [\*] stat
        - [\*] Enable display of filesystem status (-f)
    - [\*] stty
    - [\*] sync 
    - [\*] tail
    - [\*] tee
    - [\*] touch
    - [\*] tty 
    - [\*] uname
        - (Linux) Operating system name
    - [\*] uniq
    - [\*] base32
    - [\*] base64
    - [\*] wc
    - [\*] whoami
- **Console utilities**
    - [\*] clear
- **Editors**
    - [\*] awk
    - [\*] diff
        - [\*] Enable directory support
    - [\*] patch
    - [\*] sed
- **Finding utilities**
    - [\*] grep
- **Init utilities**
    - [\*] init
    - [\*] poweroff
    - [\*] reboot
- **Login/Password Management Utilities**
    - [\*] Support shadow passwords
    - [\*] addgroup
    - [\*] adduser
    - [\*] deluser
    - [\*] delgroup
    - [\*] passwd
        - (sha512) Default encryption method (passwd -a)
- **Linux Module Utilities**
    - [\*] insmod
    - [\*] lsmod
    - [\*] rmmod
- **Linux System Utilities**
    - [\*] dmesg 
    - [\*] fdisk
        - [\*]Support over 4GB disks
        - [\*] Write support
            - [\*] Support GPT disklabels
        - [\*] Support expert mode
    - [\*] xxd
    - [\*] losetup
    - [\*] lspci
    - [\*] lsusb
    - [\*] mount
        - [\*] Support -v (verbose)
        - [\*] Support specifying devices by label or UUID
        - [\*] Support lots of -o flags
        - [\*] Support /etc/fstab and -a (mount all)
    - [\*] umount
        - [\*] Support -a (unmount all)
- **Miscellaneous Utilities**
    - [\*] less
    - [\*] man
    - [\*] time
    - [\*] tree
- **Networking utilities**
    - [\*] arp
    - [\*] arping
    - [\*] ip
    - [\*] ip address
    - [\*] ip link
    - [\*] ip route
    - [\*] ping
    - [\*] traceroute
        - [\*] Enable verbose output
        - [\*] Enable -I option (use ICMP instead of UDP)
    - [\*] whois
- **Process utilities**
    - [\*] kill
    - [\*] killall
    - [\*] lsof
    - [\*] pkill
    - [\*] ps
    - [\*] pstree
    - [\*] sysctl
    - [\*] top
    - [\*] utpime
    - [\*] watch
- **Shells**
    - [\*] ash


## 7.4. Build BusyBox

```Shell
make ARCH="riscv" CROSS_COMPILE="riscv64-unknown-linux-musl-" PATH="/opt/riscv/bin:$PATH" -j$(nproc)
```

## 7.5. Install BusyBox

```Shell
make ARCH="riscv" CROSS_COMPILE="riscv64-unknown-linux-musl-" PATH="/opt/riscv/bin:$PATH" install

cp -v -r ./_install/* /opt/riscv/rootfs/
```

# 8. Virtual disk

## 8.1. Create virtual disk

```Shell
cd /opt/riscv/dev/

dd if=/dev/zero of=vda.img bs=1M count=64 # 64MB
```

## 8.2. Partition virtual disk

32MB FAT-32 partition for `/boot` (*my current install of linux & u-boot takes up 26MB space*)

Remaining spaces goes into Ext4 partition for `/` (*my current install of busybox takes up ~500KB space*)

```Shell
parted -a optimal vda.img -s mklabel gpt
parted -a optimal vda.img -s mkpart boot fat32 1MB 33MB
parted -a optimal vda.img -s mkpart rootfs ext4 33MB 100%
parted -a optimal vda.img -s print
```

## 8.3. Mount disk

```Shell
sudo losetup -f --show --partscan vda.img
```

Take note of the output - this will be your identifier! Mine is `/dev/loop0`.

## 8.4. Format partitions

```Shell
sudo mkfs.vfat -F 32 -n BOOT /dev/loop0p1

sudo mkfs.ext4 -L ROOTFS /dev/loop0p2
```

## 8.5. Mount partitions

```Shell
sudo mkdir -v -p /mnt/riscv/

sudo mount -v -t ext4 /dev/loop0p2 /mnt/riscv/

sudo mkdir -v -p /mnt/riscv/boot/

sudo mount -v -t vfat /dev/loop0p1 /mnt/riscv/boot/
```

## 8.6. Install stuff to disk

Kernel and boot loader:

```Shell
sudo cp -v /opt/riscv/boot/linux.efi /mnt/riscv/boot/linux.efi

sudo cp -v /opt/riscv/boot/u-boot.elf /mnt/riscv/boot/u-boot.elf
```

BusyBox:

```Shell
sudo cp -v -R /opt/riscv/rootfs/* /mnt/riscv/
```

Pseudo-filesystems:

```Shell
sudo mkdir -v -p /mnt/riscv/{dev,proc,sys}/
```

## 8.7. Unmount

```Shell
sudo sync

sudo umount -v -R /mnt/riscv/
```

# 9. Run it!

Launch the VM with:

```Shell
/opt/riscv/bin/qemu-system-riscv64 \
    -machine virt -smp 2 -m 4G \
    -bios "/opt/riscv/share/qemu/opensbi-riscv64-generic-fw_dynamic.bin" \
    -kernel "/opt/riscv/boot/u-boot.elf" \
    -drive file="/opt/riscv/dev/vda.img",format="raw",id="hd0" -device virtio-blk-device,drive="hd0" \
    -nographic
```

This will boot you into the U-Boot shell. We will add Linux as a boot entry, but first we must peak at the memory locations to know what is and isn't reserved:

```Shell
=> bdinfo
boot_params = 0x0000000000000000
DRAM bank   = 0x0000000000000000
-> start    = 0x0000000080000000
-> size     = 0x0000000100000000
flashstart  = 0x0000000020000000
flashsize   = 0x0000000002000000
flashoffset = 0x0000000000000000
baudrate    = 115200 bps
relocaddr   = 0x000000017f71e000
reloc off   = 0x00000000ff51e000
Build       = 64-bit
current eth = unknown
eth-1addr   = (not set)
IP addr     = <NULL>
fdt_blob    = 0x000000017eefc3f0
new_fdt     = 0x000000017eefc3f0
fdt_size    = 0x00000000000019e0
lmb_dump_all:
 memory.cnt = 0x1 / max = 0x10
 memory[0]      [0x80000000-0x17fffffff], 0x100000000 bytes flags: 0
 reserved.cnt = 0x2 / max = 0x10
 reserved[0]    [0x80000000-0x8005ffff], 0x00060000 bytes flags: 4
 reserved[1]    [0x17decd000-0x17fffffff], 0x02133000 bytes flags: 0
devicetree  = board
serial addr = 0x0000000010000000
 width      = 0x0000000000000001
 shift      = 0x0000000000000000
 offset     = 0x0000000000000000
 clock      = 0x0000000000384000
=> 
```

As you can see, `0x80000000` to `0x8005ffff` are reserved, so I will install Linux to `0x80060000`:

```Shell
fatload virtio 0:1 80060000 linux.efi
```

Now we can create our boot entry:

```Shell
setenv bootargs root=/dev/vda2 console=ttyS0 earlycon=sbi rw

setenv bootcmd 'fatload virtio 0:1 80060000 linux.efi; booti 0x80060000 - ${fdtcontroladdr}'

saveenv

boot
```

**TODO:** `saveenv` returns "Saving Environment to nowhere... not possible" - issues with flash memory? need to fix this...

# 10. System configuration (WIP)

## 10.1. Init

Mount pseudo-filesystems on boot

```Shell
cat > /etc/inittab << EOF
::sysinit:/etc/init.d/rcS
::askfirst:/bin/sh
EOF
```

```Shell
mkdir /etc/init.d/

cat > /etc/init.d/rcS << EOF
#!/bin/sh
mount -t devtmpfs nodev /dev/
mount -t proc nodev /proc/
mount -t sysfs nodev /sys/
EOF

chmod +x /etc/init.d/rcS
```

...but that won't take effect until we reboot and I don't want to do that so I'll also mount them manually for now:

```Shell
mount -t devtmpfs nodev /dev/
mount -t proc nodev /proc/
mount -t sysfs nodev /sys/
```

## 10.2. essentials

`/etc/hostname` defines our hostname

```Shell
cat > /etc/hostname << EOF
host
EOF
```

`/etc/hosts` is used to translate IP addresses to domain names

```Shell
cat > /etc/hosts << EOF
# localhost/loopback
::1 localhost
127.0.0.1 localhost
127.0.1.1 host

# blacklist
0.0.0.0 microsoft.com
EOF
```

`/etc/resolv.conf` defines DNS resolvers

```Shell
cat > /etc/resolv.conf << EOF
nameserver 9.9.9.9
nameserver 149.112.112.112
EOF
```

`/etc/passwd` contains information on users

```Shell
cat > /etc/passwd << EOF
root:x:0:0:root:/root:/bin/sh
EOF
```

`/etc/group` contains information on groups

```Shell
cat > /etc/group << EOF
root:x:0:
EOF
```

`/etc/shadow` contains user passwords in hashed format

...but this is handled by `passwd`, so we won't touch it...

## 10.3. Configure the dynamic loader

```Shell
cat > /etc/ld.so.conf << EOF
/usr/lib
/usr/local/lib
/opt/lib
EOF
```
