# 001-set-up-qemu

alpine linux QEMU VM setup from scratch

## the goal

this document covers the initial setup of an alpine linux VM in QEMU on your
Ubuntu 24.04 LTS Framework laptop. this is the foundation for experimenting
with wayland and river compositor.

we'll set up a minimal alpine installation with basic user configuration. for
GUI-optimized settings and advanced configuration, see [002-gui-optimized-vm-settings.md](./002-gui-optimized-vm-settings.md).

## prerequisites

- Ubuntu 24.04 LTS Framework laptop
- sudo-user `xy` already set up
- internet connection
- KVM support enabled in BIOS

## step 1: install QEMU on Ubuntu host

as the `xy` user on your Ubuntu host:

```bash
sudo apt update
sudo apt install -y qemu-system-x86 qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils
```

this installs QEMU, KVM support, and libvirt tools for virtualization.

## step 2: download alpine linux extended ISO

visit https://alpinelinux.org/downloads/ and download the latest extended ISO
for x86_64. the extended version includes useful tools for development work.

save it to `~/Downloads/alpine-extended-X.X.X-x86_64.iso` (replace X.X.X with
the actual version number).

you can verify the download:

```bash
ls -lh ~/Downloads/alpine-extended-*-x86_64.iso
```

## step 3: create VM disk image

create the directory for VM files and create a disk image:

```bash
mkdir -p ~/VMs
cd ~/VMs
qemu-img create -f qcow2 alpine-vm.qcow2 10G
```

this creates a 10GB qcow2 disk image. qcow2 is a copy-on-write format that
only uses disk space as needed.

**note**: for GUI work, you'll want a larger disk (20-30GB). see
[002-gui-optimized-vm-settings.md](./002-gui-optimized-vm-settings.md) for
optimized settings.

## step 4: boot from ISO and install alpine

boot QEMU with the ISO mounted to start the installation:

```bash
cd ~/VMs
ISO_FILE=$(ls -t ~/Downloads/alpine-extended-*-x86_64.iso 2>/dev/null | head -1)

qemu-system-x86_64 \
    -enable-kvm \
    -m 2048 \
    -smp 2 \
    -drive file=alpine-vm.qcow2,format=qcow2 \
    -cdrom "$ISO_FILE" \
    -boot d \
    -display gtk \
    -netdev user,id=net0,hostfwd=tcp::2222-:22 \
    -device virtio-net,netdev=net0
```

when alpine boots in the QEMU window:

1. login as `root` (no password initially)
2. run `setup-alpine`
3. follow the prompts:
   - select keyboard layout (e.g., `us` for US keyboard)
   - select hostname (or press Enter for default `alpine`)
   - configure networking:
     - choose the interface (usually `eth0`)
     - choose `dhcp` for automatic IP configuration
   - set root password (choose a secure password)
   - when prompted for disk, select `sda` (the qcow2 disk)
   - choose `sys` installation type (system disk)
   - confirm installation (type `y`)
4. wait for installation to complete
5. after installation completes, run `poweroff`

## step 5: boot installed alpine VM

after the VM powers off, boot from the installed disk:

```bash
cd ~/VMs
qemu-system-x86_64 \
    -enable-kvm \
    -m 2048 \
    -smp 2 \
    -drive file=alpine-vm.qcow2,format=qcow2 \
    -boot c \
    -display gtk \
    -netdev user,id=net0,hostfwd=tcp::2222-:22 \
    -device virtio-net,netdev=net0
```

login as `root` with the password you set during installation.

## step 6: configure alpine VM

once logged in as root, configure the system:

### create xy user

```bash
adduser xy
```

follow the prompts:
- enter password (twice)
- full name (optional, press Enter)
- room number (optional, press Enter)
- work phone (optional, press Enter)
- home phone (optional, press Enter)
- other (optional, press Enter)
- is the information correct? (type `y` and press Enter)

### enable community repository

```bash
sed -i 's|^#http://dl-cdn.alpinelinux.org/alpine/v.*/community|http://dl-cdn.alpinelinux.org/alpine/v3.22/community|' /etc/apk/repositories
```

update the version number (e.g., `v3.22`) to match your alpine version. check
your version with `cat /etc/alpine-release`.

### update package index

```bash
apk update
```

### install sudo

```bash
apk add sudo
```

### add xy to sudoers

edit the sudoers file:

```bash
visudo
```

add this line (or uncomment the wheel group line and add xy to wheel):

```
xy ALL=(ALL) NOPASSWD: ALL
```

save and exit (in vi: press `Esc`, type `:wq`, press Enter).

alternatively, add xy to the wheel group:

```bash
adduser xy wheel
```

then uncomment the wheel group line in `/etc/sudoers`:

```bash
sed -i 's/^# %wheel ALL=(ALL:ALL) NOPASSWD: ALL/%wheel ALL=(ALL:ALL) NOPASSWD: ALL/' /etc/sudoers
```

### configure networking (if needed)

if networking isn't working:

```bash
# start networking service
rc-service networking start
rc-update add networking

# configure DNS
cat > /etc/resolv.conf <<EOF
nameserver 8.8.8.8
nameserver 8.8.4.4
EOF
```

test connectivity:

```bash
ping -c 3 8.8.8.8
ping -c 3 google.com
```

### install openssh (for SSH access)

```bash
apk add openssh
rc-service sshd start
rc-update add sshd
```

## step 7: verify setup

switch to the xy user and verify sudo works:

```bash
su - xy
sudo whoami
```

you should see `root` without being prompted for a password.

test SSH access from your Ubuntu host:

```bash
# from Ubuntu host, as xy user
ssh -p 2222 xy@localhost
```

you should be able to SSH into the alpine VM.

## next steps

you now have a basic alpine VM set up with the `xy` user and sudo access.

for GUI-optimized settings (higher resolution, more memory, larger disk), see
[002-gui-optimized-vm-settings.md](./002-gui-optimized-vm-settings.md).

for advanced configuration including s6 process management, service setup, and
helper scripts, see [003-advanced-configuration.md](./003-advanced-configuration.md).

---

grainorder: 001-set-up-qemu  
timestamp: 12025-11-06  
team: teamtravel12 (Pisces ♓ / XII. Upside-Down)

*now == next + 1* 🌾
