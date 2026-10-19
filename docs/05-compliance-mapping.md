# 05 — Compliance Mapping (CIS · ISO 27001 · NIS2)

Hardening is not only technical — it also supports an organisation's **compliance** and
**risk management**. This page maps the practical measures from this lab to recognised
frameworks, showing how a technical change answers a governance requirement.

> The CIS references below are given at **category level** and described as *aligned with
> CIS Benchmark recommendations*, rather than exact control numbers, because the precise
> numbering differs by benchmark version. The ISO 27001 references point to Annex A control
> themes (2022 revision).

## Mapping table

| Measure | System | Framework area (CIS) | ISO 27001 (Annex A theme) | Risk reduced |
|---|---|---|---|---|
| Disable Guest account | Windows 11 / Server | Accounts | A.5 Access control | Unauthorized access via an unmonitored account |
| Enforce 12-char password + complexity | Windows 11 | Password policy | A.5 Access control | Weak/guessable credentials |
| Account lockout (5 attempts / 15 min) | Windows 11 | Account lockout | A.5 Access control | Brute-force / password guessing |
| Enable audit logging (logon, policy change) | Windows 11 / Server | Audit policy | A.8 Logging & monitoring | Inability to detect or investigate incidents |
| Default-deny host firewall (UFW) | Ubuntu | Host firewall | A.8 Network security | Unnecessary network exposure |
| Disable root SSH login | Ubuntu | SSH server | A.5 Access control | Direct privileged remote compromise |
| Disable unneeded services (xrdp, etc.) | Ubuntu | Services | A.8 Secure configuration | Expanded attack surface |
| Least-privilege / sudo per role | Ubuntu | Access control | A.5 Access control | Excessive privilege, lateral movement |
| Regular patching | All | Patch management | A.8 Vulnerability management | Exploitation of known vulnerabilities |
| Privilege tiering (T0/T1/T2) | Windows Server / AD | Access control | A.5 Access control | Domain-wide (Tier 0) compromise |
| Minimal Domain Admins | Windows Server / AD | Accounts | A.5 Access control | Abuse of highly privileged accounts |
| Backup & recovery (AD Recycle Bin, DSRM) | Windows Server / AD | Recovery | A.8 Business continuity | Unrecoverable loss after an incident |

## Why this matters for Swedish organisations

- **ISO 27001** is a common requirement for Swedish companies and their suppliers; being
  able to connect technical hardening to Annex A controls is exactly what an auditor or
  GRC function looks for.
- **NIS2** (now reflected in Swedish law) raises security obligations for many
  organisations, with a strong emphasis on access control, logging/monitoring, patching,
  and business continuity — all of which the measures above support directly.

This page is deliberately framework-oriented to show that I can translate hands-on
technical work into the language of **governance, risk and compliance (GRC)**, not just
system administration.
EOF
