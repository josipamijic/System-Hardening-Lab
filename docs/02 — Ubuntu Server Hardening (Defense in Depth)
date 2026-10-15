# 02 — Ubuntu Server Hardening (Defense in Depth)

**Environment:** Ubuntu Server 24.04 LTS virtual machine (Hyper-V), accessed over SSH

This documents the hardening steps applied to an Ubuntu Server VM, comparing the default
configuration against a recommended security baseline.

## Steps and evidence

### System orientation and users
Collected system info (`lsb_release`, `hostname`, `whoami`, `ip addr`). Reviewed
`/etc/passwd` to tell real accounts from service accounts — service accounts such as
`www-data`, `sshd`, `systemd-resolve`, `syslog` use `/usr/sbin/nologin` or `/bin/false`,
so they cannot be used for interactive login.

### Sudo privileges
`sudo -l` confirmed the user has unrestricted `(ALL : ALL) ALL` access. Acceptable for a
single-admin lab; in a multi-admin production environment sudo rights would be scoped to
only the commands a role requires.

### File permissions
Stepped a test file through `chmod 600, 644, 700, 777`, checking with `ls -l` each time.
Compared `/etc/passwd` (644, world-readable) with `/etc/shadow` (640, restricted).
Reading `/etc/shadow` as a normal user correctly returned **Permission denied**.

### Services and open ports
Listed active services (`systemctl`) and listening ports (`ss -tulpn`). Found **20
services running by default**, including `xrdp` (Remote Desktop) and `ModemManager`,
neither needed on a headless server. `xrdp` was listening on **port 3389 across all
interfaces (0.0.0.0)**, not just localhost. Both `ModemManager` and `xrdp` were stopped
and disabled — afterwards only port 22 (SSH) and local DNS remained listening.

### UFW firewall
UFW was **inactive by default**. Following correct change-management order, ran
`ufw allow OpenSSH` **before** enabling the firewall (to avoid losing remote access),
then enabled UFW with default **deny incoming / allow outgoing**, permitting only SSH.

### SSH hardening
Backed up the config first (`sshd_config.backup`). Found `PermitRootLogin`,
`PubkeyAuthentication`, `PasswordAuthentication` all at commented-out defaults, with a
separate file (`50-cloud-init.conf`) overriding `PasswordAuthentication` to yes. With no
SSH key pair configured, left password auth enabled and changed only
**`PermitRootLogin` to `no`**. Verified syntax with `sshd -t`, restarted the service, and
confirmed access still worked from a **second independent SSH session** before trusting
the change.

### AppArmor
`aa-status` showed several hundred profiles defined, but only `rsyslogd` actively in
enforce mode. No changes made.

### Patch status and logging
`apt list --upgradable` reported 182 packages pending (some security updates).
`unattended-upgrades` was installed and active. The full update was **not applied** this
session (time constraints + restart risk) and documented as an open item. Reviewed
`journalctl -u ssh` and `/var/log/auth.log` — all logins and the service restart were
correctly attributed to the user, not root.

## Top risks identified & defense in depth

Key scenario reasoning from the exercise:

- **"Just give me root, it's easier"** — individual accounts + sudo are preferable:
  every privileged action is logged under a specific user, and a compromise is limited to
  that account's sudo rules rather than the whole system.
- **`chmod 777` as a quick fix** — widens access to everyone; the correct fix is to grant
  only the specific user/group (e.g. `www-data`) the minimum permission needed.
- **"50 updates pending, but it works"** — a working system is not a secure one; patching
  should be a scheduled routine, not deferred until a failure forces it.
- **SSH open to the internet with passwords** — move to key auth (disable passwords once
  keys exist), restrict the SSH UFW rule to admin IP ranges, and consider `fail2ban`.

**Defense in Depth on this server:** UFW filters traffic first → SSH controls
authentication → sudo governs what an authenticated user may do → file permissions
control direct data access → AppArmor restricts process behaviour → logging supports
detection after the fact. If one layer fails (e.g. a leaked password), the others still
limit the damage.

## Recommended baseline (top 5)

1. Disable direct root SSH login (`PermitRootLogin no`).
2. Enable UFW with default-deny incoming, allowing only required services.
3. Disable unnecessary services (remote desktop, unused GUI components).
4. Establish a regular, scheduled patch cycle.
5. Move to SSH key-based authentication and disable password auth once keys are in place.

## Note on limitations

Performed in a Hyper-V VM over SSH. The VM's IP changed once mid-session after the host
slept and the VM got a new DHCP lease, requiring a reconnect. The full package upgrade
(182 pending) was not performed this session due to time and restart risk — documented
rather than left unmentioned.
