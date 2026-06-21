# Unitree Go1 Pro — Almost-Full Backup 🐕‍🦺

A near-complete software/config backup of a **Unitree Go1 Pro**, pulled off a real unit.
Sharing it so others have a reference image of the Go1 Pro stack — and because I'm
**missing one piece and could really use help** (see below).

## 🙏 PLEA: I need the head Nano (`192.168.123.13`)

My Go1 Pro's **head Jetson Nano (`192.168.123.13`)** is dead/absent — it never appears
on the internal network, so my **two front (face + chin) cameras don't work** and I have
no way to back it up. Everything else I captured; this one board I can't.

**If you have a working Go1 (Pro or EDU) with a live `.13` Nano, please send me a backup
of it** — ideally a tar of `/home/unitree/Unitree` + `/etc` from `192.168.123.13`, or a
full SD/eMMC image. Open an issue or PR here. It would let me restore (or at least
understand) the front-camera stack. Honestly not even sure it's recoverable on my unit,
but a reference from a healthy `.13` would be a huge help. 🙏

## What's in here

| File | Board | Notes |
|---|---|---|
| `pi-backup-SANITIZED.tgz` | Raspberry Pi CM4 (`.161`) — main | `/home/pi/Unitree` (sportMode, webMonitor, 07obstacle, appTransit, utrack…), `UnitreeUpgrade`, `/etc`, `/usr/local/bin`, boot config |
| `nano14-backup-SANITIZED.tar.xz` | Jetson Nano `.14` — left/right cameras | `/home/unitree/Unitree` + `/etc` |
| `nano15-backup-SANITIZED.tar.xz` | Jetson Nano `.15` — belly camera + obstacle map | `/home/unitree/Unitree` + `/etc` |
| `firmware/` | MCU co-processor | `CoH017_35` / `CoH019_35` STM32 firmware + flasher (from the community) |
| `pi-packages.txt` | — | `dpkg -l` from the Pi |
| `manifest.txt` | — | versions + board status at capture time |

**Boards:** `.161` Pi · `.10` MCU (STM32, not a filesystem) · `.14`/`.15` Nanos (here) ·
`.13` Nano (**DEAD/MISSING — the whole point of this plea**).

## 🔒 Sanitized — no secrets included
Removed before publishing: `wpa_supplicant.conf` (WiFi passwords), `/etc/shadow` &
`gshadow` (password hashes), `ssh_host_*_key` (private SSH keys), `/etc/ssl/private`,
and `passwd.sh`. You'll need to recreate those on restore. Default Unitree login is
`pi` / `123` and `unitree` / `123`.

## Restore (per board)
```bash
# gzip tarball (Pi):
sudo tar xzf pi-backup-SANITIZED.tgz -C /target
# xz tarballs (Nanos):
sudo tar xJf nano14-backup-SANITIZED.tar.xz -C /target
```
Inside, the original pre-edit configs are preserved as `*.bak-claude` so you can diff/roll back.

## Known-good tweaks already baked in
- Nanos auto-sync their clock from the Pi at boot (they have no RTC) via `camerarosnode.sh`
  — fixes sensor-fusion timestamp mismatches.
- `node_pcl_process` re-enabled in `07obstacle` (it ships commented out) — needed for the
  obstacle/point-cloud map.
- Pi `wlan0` set up as a home-WiFi client with a boot service + watchdog.
- Hotspot moved to 5 GHz ch44 + `require_ht/vht=0` so modern phones can join.

*Not included: full bit-for-bit `dd` disk images (this is the software/config layer only).*
