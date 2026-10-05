# Proxmox

## What runs where

One Proxmox VE host carries everything:

- **A general-purpose Linux VM** running the media/personal-cloud stack: a media server (Jellyfin) with hardware-accelerated transcoding via the host's integrated GPU, a torrent client for pulling content in, and a self-hosted personal cloud (Nextcloud) for file sync/backup, all pointed at the NAS for storage rather than local disk.
- **A lightweight container** running a small web server (nginx), used for simple internal hosting needs (serving a couple of static assets for a dashboard, for instance) — overkill to spin up a full VM for.
- **A second lightweight container** running the secondary Pi-hole instance described in [network.md](network.md).
- **A "fun/experimentation" VM**, kept deliberately separate from anything that needs to be reliable, for trying things that might break.

Keeping the "needs to actually work" services (media, cloud, DNS) on separate containers/VMs from the "I'm just trying stuff" VM means breaking the experimental one never takes down anything that matters.

## Two gotchas that actually caused outages

### 1. Virtualization disabled in firmware

On this class of tiny business PC, Intel VT-x (hardware virtualization) ships **disabled** in the BIOS by default, which blocks Proxmox from starting any VM at all (you'll get a KVM-related startup error). On the specific board used here it was filed under **Advanced → CPU Setup**, not under Security where you'd expect it. If VMs refuse to start and the error mentions KVM, check the BIOS before assuming it's a Proxmox config problem.

### 2. NIC hanging under load

Symptom: the whole host became unreachable for a few minutes — every service on it dropped — while the OS itself stayed up and recovered on its own shortly after. The Proxmox system log showed the onboard NIC driver repeatedly logging a "hardware unit hang" every couple of seconds.

Cause: a known issue with the onboard Intel NIC driver (`e1000e`) and certain hardware offload features (TSO/GSO/GRO) under sustained load.

Fix, run from the Proxmox web Shell:

```sh
ethtool -K <your-nic-name> tso off gso off gro off
```

Verify with `ethtool -k <your-nic-name> | grep offload`. To make it survive a reboot, back up `/etc/network/interfaces` first, then add a `post-up` line calling the same `ethtool` command under that interface's stanza.

## Other things worth knowing

- If a VM's hostname resolves to a stale IP in Proxmox's own System → Hosts page after you re-address anything, fix it there directly — it won't fix itself and can cause confusing cluster/name-resolution issues later.
- Set "Start at boot" on anything that actually needs to come back up automatically after a host reboot — it's off by default per VM/container, and it's easy to forget until a reboot leaves something down that you have to notice and start by hand.
- Pasting into the Proxmox web-based Shell (noVNC) can inject junk escape characters on some setups. If a pasted command looks mangled, clear the line and either type it by hand or use the terminal's right-click paste instead of a keyboard shortcut.
