# Personal cloud (Nextcloud)

The "cloud backup" for this homelab is self-hosted and open source: Nextcloud, with every file stored on the NAS. No paid cloud service.

## How it is put together

- Nextcloud runs as a Docker Compose stack on the same general-purpose Linux VM that hosts the media server: one container for the app (the Apache image) and one for the database (MariaDB).
- It is published on its own port on that VM, picked after checking what the media server and the torrent client already use.
- Its data folder is **not** on the VM's disk. A dedicated folder on the NAS is mounted on the VM over SMB/CIFS and bind-mounted into the container as Nextcloud's data directory. The VM only runs the program. Delete the VM and the files are still on the NAS.

## The part that takes a minute to get right

Nextcloud's web server runs as the `www-data` user (uid and gid 33 in the official image) and refuses to start if it does not own its data folder or if other users can read it. A CIFS mount takes its ownership from the mount options, not from the files, so the mount line in `/etc/fstab` needs:

- `uid=33,gid=33` so the folder belongs to `www-data` as far as the container can tell
- `file_mode=0770,dir_mode=0770` so nobody else can read it
- `nofail` so the VM still boots if the NAS is not reachable yet

This is a second mount, separate from the one the media server uses, because that one is owned by the normal login user.

## After it was running

- Added to the uptime monitor and given a tile on the dashboard, like every other service.
- Phone app pointed at it and confirmed working.
- First real use: holding the router's exported settings file, which puts a copy of it on the NAS.
- **Cold-start test passed.** The VM was shut down fully (by accident, it was meant to be a reboot) and started again. The media server, the torrent client and Nextcloud all came back on their own with the NAS mounts in place. Worth doing on purpose once.

## A small thing that made the whole job easier

The VM's graphical console in the hypervisor's web page is awkward for long commands and pasting into it is unreliable. Installing an SSH server on the VM and working from a normal terminal on another machine made the Compose setup far less painful.

## Not built

- A reusable upload link so friends can drop music or films straight onto the NAS. It is an idea for later, including the open question of whether uploads should land in a review folder or go straight into the media library.
