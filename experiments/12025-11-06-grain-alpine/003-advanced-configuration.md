# 003-advanced-configuration

advanced alpine VM configuration with s6 process management

## prerequisites

complete both [001-set-up-qemu.md](./001-set-up-qemu.md) and
[002-gui-optimized-vm-settings.md](./002-gui-optimized-vm-settings.md) first.
this document covers advanced configuration that builds on the optimized setup.

## s6 process management

alpine uses OpenRC by default, which works well. however, s6 provides a more
intentional process supervision model that aligns with alpine's minimalism and
musl's philosophy.

s6 is a process supervision suite designed for unix systems. it provides:
- reliable process supervision (automatic restarts)
- dependency management
- logging and monitoring
- minimal resource usage
- explicit service definitions

### why s6 on alpine?

- **musl compatibility**: s6 works well with musl libc
- **minimalism**: s6 is lightweight and explicit
- **reliability**: process supervision ensures services stay running
- **intentionality**: explicit service definitions match alpine's philosophy

### installing s6

as root or with sudo:

```bash
# add s6 repository (if not in main repos)
apk add s6 s6-rc s6-linux-init
```

s6-linux-init provides init system integration, while s6-rc manages service
dependencies and s6 provides the core supervision.

### s6 service structure

s6 services live in `/etc/s6/sv/`. each service has:
- `run` script: starts the service
- `finish` script (optional): cleanup when service stops
- `type` file: service type (oneshot, longrun, etc.)
- `dependencies` file (optional): service dependencies

### example: s6 service for networking

create a simple networking service:

```bash
mkdir -p /etc/s6/sv/networking

cat > /etc/s6/sv/networking/run <<'EOF'
#!/bin/sh
exec /sbin/ifup -a
EOF

chmod +x /etc/s6/sv/networking/run

cat > /etc/s6/sv/networking/type <<'EOF'
oneshot
EOF
```

### example: s6 service for SSH

```bash
mkdir -p /etc/s6/sv/sshd

cat > /etc/s6/sv/sshd/run <<'EOF'
#!/bin/sh
exec /usr/sbin/sshd -D
EOF

chmod +x /etc/s6/sv/sshd/run

cat > /etc/s6/sv/sshd/type <<'EOF'
longrun
EOF
```

### enabling s6 services

use s6-rc to manage services:

```bash
# compile service database
s6-rc-compile /etc/s6/rc/compiled /etc/s6/sv

# enable a service
s6-rc -u change networking
s6-rc -u change sshd
```

### s6 init integration

to use s6 as init system (replacing OpenRC), install s6-linux-init and
configure:

```bash
# install s6-linux-init
apk add s6-linux-init

# create s6 init structure
s6-linux-init-maker -1 -c /etc/s6/rc/compiled /run/s6-linux-init

# set s6 as init (requires careful configuration)
# note: this is advanced and may require system modifications
```

**note**: replacing OpenRC with s6 as init is advanced. for most use cases,
running s6 services alongside OpenRC is sufficient.

## directory structure

create a consistent directory structure that mirrors your Ubuntu host:

```bash
# as xy user in alpine VM
mkdir -p ~/github ~/kae3g ~/gitlab ~/experiments
```

this provides consistency across environments.

## helper scripts

create helper scripts for common tasks.

### resize-disk.sh (on Ubuntu host)

if you need to resize the VM disk:

```bash
cat > ~/VMs/resize-disk.sh <<'EOF'
#!/bin/bash
# resize alpine VM disk

VM_DIR="$HOME/VMs"
VM_DISK="$VM_DIR/alpine-vm.qcow2"
SIZE="${1:-+20G}"

if [ ! -f "$VM_DISK" ]; then
    echo "Error: VM disk not found at $VM_DISK"
    exit 1
fi

echo "Resizing disk by $SIZE..."
qemu-img resize "$VM_DISK" "$SIZE"
echo "Disk resized. You'll need to resize the partition inside the VM."
EOF

chmod +x ~/VMs/resize-disk.sh
```

### vm-status.sh (on Ubuntu host)

check VM status:

```bash
cat > ~/VMs/vm-status.sh <<'EOF'
#!/bin/bash
# check alpine VM status

VM_DIR="$HOME/VMs"
VM_DISK="$VM_DIR/alpine-vm.qcow2"

if [ -f "$VM_DISK" ]; then
    echo "VM disk: $VM_DISK"
    qemu-img info "$VM_DISK"
    echo ""
    echo "To check if VM is running:"
    echo "  ps aux | grep qemu"
    echo ""
    echo "To SSH into VM:"
    echo "  ssh -p 2222 xy@localhost"
else
    echo "VM disk not found at $VM_DISK"
fi
EOF

chmod +x ~/VMs/vm-status.sh
```

## networking details

the default configuration uses QEMU's user-mode networking (slirp):

- NAT networking (VM can reach internet, host can reach VM)
- automatic DHCP for the VM
- port forwarding: host port 2222 → VM port 22 (SSH)

this works out of the box on Ubuntu without additional configuration.

### s6 networking service

with s6, you can create explicit networking service:

```bash
mkdir -p /etc/s6/sv/networking

cat > /etc/s6/sv/networking/run <<'EOF'
#!/bin/sh
exec /sbin/ifup -a
EOF

chmod +x /etc/s6/sv/networking/run

cat > /etc/s6/sv/networking/type <<'EOF'
oneshot
EOF
```

## service management with s6

s6 provides explicit service management:

```bash
# list services
s6-svstat /etc/s6/sv/*

# start a service
s6-svc -u /etc/s6/sv/sshd

# stop a service
s6-svc -d /etc/s6/sv/sshd

# restart a service
s6-svc -r /etc/s6/sv/sshd

# view service logs
s6-log /etc/s6/sv/sshd/log
```

## troubleshooting

### VM won't boot

- make sure KVM is enabled in BIOS
- check that `qemu-system-x86_64` is installed: `which qemu-system-x86_64`
- verify disk image exists: `ls -lh ~/VMs/alpine-vm.qcow2`
- check QEMU logs for errors

### networking not working

- check that networking service is running: `rc-service networking status` or `s6-svstat /etc/s6/sv/networking`
- verify DNS: `ping 8.8.8.8` and `ping google.com`
- check `/etc/resolv.conf` has nameservers
- ensure networking is enabled: `rc-update add networking` or enable with s6

### can't SSH to VM

- make sure VM is running
- check port forwarding: `ssh -p 2222 xy@localhost`
- verify SSH service: `rc-service sshd status` or `s6-svstat /etc/s6/sv/sshd`
- ensure SSH is enabled: `rc-update add sshd` or enable with s6

### sudo not working

- verify xy is in wheel group: `groups xy`
- check sudoers: `visudo -c`
- ensure sudo package is installed: `apk list sudo`
- test with: `sudo whoami` (should return `root` without password)

### QEMU display issues

if the GTK display doesn't work, try using VNC instead:

```bash
qemu-system-x86_64 \
    -enable-kvm \
    -m 4096 \
    -smp 4 \
    -drive file=alpine-vm.qcow2,format=qcow2 \
    -boot c \
    -display vnc=:1 \
    -vga virtio \
    -netdev user,id=net0,hostfwd=tcp::2222-:22 \
    -device virtio-net,netdev=net0
```

then connect with a VNC client to `localhost:5901`.

### s6 service issues

- check service status: `s6-svstat /etc/s6/sv/service-name`
- view service logs: `s6-log /etc/s6/sv/service-name/log`
- verify run script is executable: `ls -l /etc/s6/sv/service-name/run`
- check service type: `cat /etc/s6/sv/service-name/type`

## next steps

with s6 process management configured, you're ready to experiment with wayland
and river compositor. the explicit service management provided by s6 aligns
well with alpine's minimalism and musl's philosophy.

for wayland and river setup, see the other documents in this experiment folder.

---

grainorder: 003-advanced-configuration  
timestamp: 12025-11-06  
team: teamtravel12 (Pisces ♓ / XII. Upside-Down)

*now == next + 1* 🌾

