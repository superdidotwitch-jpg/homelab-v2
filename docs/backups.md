# Backups

## What gets backed up

- **Proxmox-level backups**: a scheduled Proxmox backup job covers every VM and container on the hypervisor host, written to the NAS over SMB/CIFS (added as a Proxmox storage target, content type "Backup"). Selection mode is set to "All," specifically so a newly created VM or container is automatically included going forward instead of being silently left out until someone remembers to add it.
- **DNS box backup**: the DNS/ad-blocking box runs its own nightly backup, outside of Proxmox (it isn't a VM) — a small cron job that exports the Pi-hole configuration and tars up the other self-hosted app data on that box, then copies both to the NAS, keeping a rolling window of the most recent copies rather than keeping everything forever.
- **Router configuration**: periodically export the router's settings file by hand and drop it in the personal cloud (see below) so a router replacement doesn't mean reconfiguring everything from memory.

## Where backups land

Everything above lands in one place on the NAS, so there's a single location to point a second, off-site copy at later rather than several scattered backup locations.

## Restore testing

The rule used here: **never test a restore on the live machine**, especially anything the rest of the house actually depends on (DNS being the obvious one). Instead:

1. Restore the backup as a *new*, throwaway VM/container with a different ID/name.
2. Confirm it actually comes up and serves what it's supposed to.
3. Destroy the throwaway copy. The real machine was never touched.

This is cheap to do (a Proxmox restore-to-new-ID takes a couple of minutes) and it's the only way to actually know a backup is good before you need it for real. A backup you've never restored from is a guess, not a backup.

## Honest open item

Restore testing for the DNS box's nightly backup specifically (as opposed to the Proxmox VM/container backups) hadn't been run yet as of this write-up — it's on the list, not done. Worth saying plainly rather than implying everything here has been fully verified.
