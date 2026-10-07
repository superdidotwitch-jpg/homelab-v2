# Web server

A deliberately small one: nginx in a lightweight Debian container on the hypervisor, serving one static page.

## Why it exists

Partly as an exercise in deploying a web server from nothing, and partly because it turned out to be useful. The dashboard needed a logo and a background picture, and pointing it at image links on someone else's site means they break whenever that site changes. Hosting both files on this container means they never expire.

## Notes

- A container, not a VM. A static site does not need its own kernel, and a container this size starts in a couple of seconds.
- Files are copied in from the hypervisor host with `pct push <container-id> <local-file> /var/www/html/<name> --perms 644`, so the container does not need SSH or any file-sharing service of its own.
- It gets its address from DHCP with a reservation on the router, the same as everything else. Its first address was a hand-picked static one that collided with a device already on the network. See [incidents.md](incidents.md).
- Monitored and on the dashboard, with the hypervisor's CPU and memory figures for the container shown on its tile.
- It was also the first machine used for a restore test. See [backups.md](backups.md).
