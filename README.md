# System Hardening Lab — Windows 11, Ubuntu Server & Windows Server 2025 (AD)

> **In 30 seconds:** I took three freshly installed systems and made them more secure
> ("hardening"). For each one I first documented its **default state**, then applied
> security measures based on recognised standards (**CIS Benchmarks** / security
> baselines), then documented the **result** — a clear before/after. I also promoted a
> Windows Server to a **Domain Controller** and analysed how that changed its attack
> surface. The project shows I can assess a system's security, improve it deliberately,
> and explain *why* each change reduces risk.

---

## What is this?

**Hardening** means reducing a system's attack surface: turning off what isn't needed,
tightening accounts and passwords, enabling firewalls and logging, and restricting
access. A freshly installed system is *usable* out of the box, but **not secure** out of
the box — hardening is a separate, deliberate step. This lab proves that point across
three different systems and documents exactly what had to change.

Everything was done in isolated Hyper-V virtual machines.

## The three systems

| # | System | Focus | Standard |
|---|---|---|---|
| 1 | **Windows 11** | Accounts, password & lockout policy, audit logging, firewall, BitLocker | CIS Benchmarks |
| 2 | **Ubuntu Server 24.04** | SSH, UFW firewall, unnecessary services, file permissions, AppArmor, patching | Security baseline / defense in depth |
| 3 | **Windows Server 2025 + Active Directory** | Baseline, promotion to Domain Controller, attack-surface analysis | CIS / Microsoft Security Baseline |

## Method (applied to every system)

For each checkpoint I used the same disciplined workflow:

```
Current state  →  Risk  →  Decision  →  Change  →  Verification  →  Result
```

This is what separates real hardening from "just running some commands": every change is
justified by a risk, verified afterwards, and limitations are documented honestly rather
than hidden.

## Highlights

**Windows 11 (CIS Benchmarks)**
- Disabled the built-in Guest account (was enabled by default).
- Enforced a 12-character minimum password length with complexity — and confirmed a weak
  password was rejected.
- Set an account lockout policy (5 attempts, 15-minute lockout).
- Enabled audit logging for logon and policy-change events, then located a real logon in
  Event Viewer (Event ID 4624).
- Documented a real limitation: BitLocker could not be enabled because of mounted
  ISO drives — recorded rather than ignored.

**Ubuntu Server (defense in depth)**
- Enabled the UFW firewall with a default-deny inbound policy (allowing SSH first, to
  avoid locking myself out — correct change-management order).
- Disabled root SSH login and verified access still worked from a second session before
  trusting the change.
- Found and disabled unnecessary services — including a Remote Desktop service (xrdp)
  listening on **all** interfaces on a headless server.
- Produced a 10-point recommended baseline and mapped findings to the **CIA triad**.

**Windows Server 2025 → Domain Controller**
- Documented a full baseline *before* installing Active Directory.
- Promoted the server to a Domain Controller and measured what changed: running services,
  new listening ports (Kerberos 88, LDAP 389/636, DNS 53, …), firewall rules, and log
  sources.
- Analysed the **increased attack surface** and explained why a DC is a **Tier 0** system
  whose compromise affects the entire domain — with concrete hardening recommendations
  (minimal Domain Admins, privilege tiering, redundant DC, backup/recovery).

## Documentation

- [`docs/01-windows11-hardening.md`](docs/01-windows11-hardening.md)
- [`docs/02-ubuntu-server-hardening.md`](docs/02-ubuntu-server-hardening.md)
- [`docs/03-windows-server-ad.md`](docs/03-windows-server-ad.md)
- [`docs/04-lessons.md`](docs/04-lessons.md) — key takeaways and recommended baselines

## Key skills demonstrated

System hardening · CIS Benchmarks · Active Directory security · Defense in depth ·
Least privilege · Attack-surface analysis · Windows & Linux administration · Firewalls
(UFW / Windows Defender Firewall) · SSH hardening · Audit logging · CIA triad

## Note

All work was performed in isolated Hyper-V lab virtual machines using fictional data.
These write-ups are my own documentation of practical hardening work.

---

*Author: Josipa — cybersecurity student building a hands-on security portfolio.*
