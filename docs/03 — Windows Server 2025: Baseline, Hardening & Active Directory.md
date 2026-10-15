# 03 — Windows Server 2025: Baseline, Hardening & Active Directory

**Environment:** Windows Server 2025 virtual machine (Hyper-V), later promoted to a
Domain Controller for the fictional domain `lab.local`

This is the most advanced part of the project: establishing a full baseline, applying
basic hardening, then promoting the server to a **Domain Controller** and analysing how
that changes its security posture.

## Part 1 — Baseline and basic hardening (before AD)

Documented the server's starting state before any change: OS version, hostname, users,
roles/features, listening ports, firewall, and Defender status.

Key findings:
- **Firewall:** all three profiles (Domain/Private/Public) on by default; Inbound Block,
  Outbound Allow.
- **Microsoft Defender:** Antivirus and real-time protection enabled, signatures current.
  QuickScan and FullScan both run — no threats found.
- **Guest account:** already disabled by default. **Administrators group:** only the
  built-in Administrator. Both already in a secure state.
- **Patching:** the server was **missing 5 security updates** despite a fresh install,
  including a cumulative monthly security update — all installed, with restarts.
- **Logging:** reviewed the Security log (~11,000 events), including Event ID 4624
  (logon), 4672 (special privileges), 5379 (credential read). "Audit Failure" entries
  (5038 code integrity, 5061 crypto) coincided with Windows Update and were assessed as
  **not** a security incident.

Hardening used the model **Current state → Risk → Decision → Change → Verification →
Result** for each checkpoint (RDP kept disabled, firewall verified, Defender verified,
updates installed).

## Part 2 — Promotion to Domain Controller

Before installing Active Directory, formed five hypotheses — more services, new ports
(DNS/Kerberos/LDAP/RPC), DNS role auto-installed, changed user model, more firewall rules
— **all five later confirmed.**

Installed AD DS and promoted via PowerShell:

```powershell
Install-ADDSForest -DomainName "lab.local" -DomainNetbiosName "LAB" -InstallDNS -Force
```

(Two expected lab warnings appeared — no parent DNS zone for delegation, and no static
IP — neither blocked the install.)

## Part 3 — What changed, and why it matters

Measured before/after promotion:

| Area | Before | After |
|---|---|---|
| Server role | Member server (workgroup) | **Domain Controller** |
| Running services | baseline | 102 |
| Listening ports | local/RPC only | + 53 (DNS), 88 (Kerberos), 389/636 (LDAP/LDAPS), 464, 3268/69 (Global Catalog), 9389 |
| Firewall rules | baseline | 224 active |
| User model | local SAM (4 accounts) | domain-based (+ **krbtgt**) |
| Log sources | Security only | + Directory Service, DNS Server |

**Attack-surface analysis:** the surface clearly grew — but not necessarily "less
secure", because the server gained an intended function (domain identity). The new
services (Kerberos, LDAP, DNS) are required for it. What increases is the **need to
harden and monitor** the larger surface.

**Criticality:** before, a compromise hit one machine. After promotion, the DC handles
identity, authentication, admin groups and Group Policy for the **entire domain** — a
compromise could mean control over every domain account and resource. This is why a DC is
a **Tier 0** system.

## Hardening recommendations after AD

1. Keep Domain Admins membership minimal and review it regularly.
2. Separate everyday and administrative accounts.
3. Introduce **privilege tiering** (T0 = DC/AD, T1 = servers, T2 = clients).
4. Set a static IP on the DC.
5. Centralise logging/SIEM for Security, Directory Service and DNS logs.
6. Enable AD Recycle Bin; document and test backup/recovery (including the DSRM password).
7. Prefer Kerberos; reduce NTLM where possible.
8. Plan a **second Domain Controller** for redundancy.

**Key principles applied:** One Server – One Purpose (a DC should not also host IIS/SQL/a
file server); Least Privilege (not everyone should be Domain Admin); restrict RDP to a
management network / jump host; and backup matters because prevention reduces the
likelihood of an incident, but recovery determines whether the environment survives one.

A Domain Controller should follow a **stricter baseline** (Microsoft Windows Server 2025
Security Baseline or CIS Benchmark) than an ordinary member server — always tested before
production, since overly strict settings can break AD functionality (Kerberos,
replication).

## Note on limitations

Performed in a Hyper-V VM. The network adapter was moved from WAN Switch to Default Switch
due to unstable connectivity over the host's WiFi. Some baseline screenshots (raw command
output for a few sections) were not retained and can be regenerated if needed.
