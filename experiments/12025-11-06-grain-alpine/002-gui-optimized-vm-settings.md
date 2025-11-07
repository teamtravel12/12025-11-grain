# 002-gui-optimized-vm-settings

optimized QEMU settings for GUI work (wayland, river, etc.)

## prerequisites

complete [001-set-up-qemu.md](./001-set-up-qemu.md) first. this document
assumes you have a basic alpine VM running with the `xy` user configured.

## why optimize?

for GUI work, especially with wayland compositors like river, you'll want:
- higher resolution for better display quality
- more memory for compositor and applications
- more disk space for building from source
- better graphics performance with virtio-vga

alpine's default OpenRC init system works, but for a more intentional process
management approach, we'll integrate s6 later (see
[003-advanced-configuration.md](./003-advanced-configuration.md)).

## recommended settings

### memory

for GUI work, increase memory from 2GB to 4-8GB:

```bash
-m 4096  # 4GB (good starting point)
# or
-m 8192  # 8GB (if you have enough host RAM)
```

### disk space

increase disk size from 10GB to 20-30GB:

```bash
# create larger disk image (if starting fresh)
qemu-img create -f qcow2 alpine-vm.qcow2 30G
```

if you already have a 10GB disk, you can resize it:

```bash
# resize existing disk (shut down VM first)
qemu-img resize ~/VMs/alpine-vm.qcow2 +20G

# then inside alpine VM, resize the partition:
# (this requires additional tools - see 003 for details)
```

### display resolution

for better GUI experience, use virtio-vga for improved performance:

```bash
-vga virtio
```

resolution can be configured inside alpine after wayland is set up. virtio-vga
provides better performance than standard VGA and supports higher resolutions.

### CPU cores

for building from source, more CPU cores help:

```bash
-smp 4  # 4 cores (adjust based on your host)
```

## updated QEMU command

here's an updated command with GUI-optimized settings:

```bash
qemu-system-x86_64 \
    -enable-kvm \
    -m 4096 \
    -smp 4 \
    -drive file=~/VMs/alpine-vm.qcow2,format=qcow2 \
    -boot c \
    -display gtk \
    -vga virtio \
    -netdev user,id=net0,hostfwd=tcp::2222-:22 \
    -device virtio-net,netdev=net0
```

## updated helper script

update `~/VMs/qemu-vm-start.sh` on your Ubuntu host:

```bash
cat > ~/VMs/qemu-vm-start.sh <<'EOF'
#!/bin/bash
# QEMU Alpine VM startup script (GUI-optimized)

VM_DIR="$HOME/VMs"
VM_DISK="$VM_DIR/alpine-vm.qcow2"

if [ ! -f "$VM_DISK" ]; then
    echo "Error: VM disk not found at $VM_DISK"
    echo "Please install Alpine first using 001-set-up-qemu.md"
    exit 1
fi

qemu-system-x86_64 \
    -enable-kvm \
    -m 4096 \
    -smp 4 \
    -drive file="$VM_DISK",format=qcow2 \
    -boot c \
    -display gtk \
    -vga virtio \
    -netdev user,id=net0,hostfwd=tcp::2222-:22 \
    -device virtio-net,netdev=net0
EOF

chmod +x ~/VMs/qemu-vm-start.sh
```

## notes

- memory: allocate based on your host RAM. if you have 16GB host RAM,
  4-8GB for the VM is reasonable. if you have 8GB host RAM, 4GB might
  be the limit.

- disk: qcow2 is copy-on-write, so it only uses space as needed. 30GB
  is a safe size that won't immediately consume 30GB on your host.

- resolution: the virtio-vga driver provides better performance than
  standard VGA. resolution can be configured inside the guest OS after
  wayland is set up.

- process management: alpine uses OpenRC by default. for more intentional
  process supervision with s6, see [003-advanced-configuration.md](./003-advanced-configuration.md).

## cursor appimage on alpine

**short answer: probably not without workarounds.**

cursor appimage is built for glibc-based distributions (ubuntu, debian,
fedora, etc.). alpine uses musl libc, which is incompatible.

**options:**

1. **use cursor on host**: run cursor on your ubuntu host and connect to
   alpine VM via SSH for development. this is the simplest approach.

2. **glibc compatibility**: install glibc compatibility layer on alpine
   (not recommended, adds complexity and defeats alpine's minimalism).

3. **container approach**: run cursor in a glibc-based container on
   alpine (complex, may not work well with wayland).

4. **different editor**: use a text editor that works on alpine, such as:
   - neovim (lightweight, powerful)
   - helix (rust-based, modern)
   - emacs (if you're into that)
   - vscode alternatives that work on musl

**recommendation**: use cursor on your ubuntu host for editing, and use
alpine VM for running/testing your wayland/river experiments. you can
mount the alpine VM's filesystem or use SSH/rsync to sync files.

## next steps

with GUI-optimized settings configured, you're ready for advanced
configuration including s6 process management, service setup, and helper
scripts. see [003-advanced-configuration.md](./003-advanced-configuration.md).

---

grainorder: 002-gui-optimized-vm-settings  
timestamp: 12025-11-06  
team: teamtravel12 (Pisces ♓ / XII. Upside-Down)

*now == next + 1* 🌾
