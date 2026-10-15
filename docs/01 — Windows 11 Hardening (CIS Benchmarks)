# 01 — Windows 11 Hardening (CIS Benchmarks)

**Environment:** Windows 11 virtual machine (Hyper-V)

This documents the hardening steps applied to a Windows 11 VM, comparing the default
configuration against CIS Benchmark recommendations. For each item I record the starting
state, the change performed, and the resulting state.

## Steps and evidence

### Guest account
Checked the built-in Guest account in Local Users and Groups (`lusrmgr.msc`). It was
**enabled by default** and was then disabled ("Account is disabled").

### Administrators group
Reviewed the membership of the local Administrators group (no changes made). Members:
Administrator, Josipa.

### Password policy
Using Local Security Policy (`secpol.msc` → Account Policies → Password Policy), set the
**minimum password length to 12 characters** and enabled **complexity requirements**.
Ran `gpupdate /force` to apply, and a test account confirmed a short/weak password was
rejected.

### Account lockout policy
Configured a threshold of **5 invalid logon attempts**, a **15-minute lockout duration**,
and a 15-minute reset of the lockout counter.

### Firewall profiles
Checked firewall status (Windows Security → Firewall & network protection). All three
profiles — Domain, Private, Public — were **already on by default**. No rule changes made.

### BitLocker
Attempted BitLocker activation. It **could not be completed** because two DVD/ISO drives
were mounted on the VM. Verified with `manage-bde -status`: Protection Status Off, 0%
encrypted. Documented as an environment limitation rather than ignored.

### Security logging
Configured Audit Policy (`secpol.msc` → Local Policies → Audit Policy), enabling
**Audit logon events** and **Audit policy change** for both Success and Failure.
Generated a logon event and located it in Event Viewer (**Event ID 4624**, successful
logon).

## Reflection

The most surprising part was how many important security settings are **not enabled by
default**: the Guest account was still active, the password policy allowed short/simple
passwords, and detailed auditing was off. A freshly installed Windows 11 system is
usable out of the box, but **not secure out of the box** — hardening is a separate,
deliberate step.

The biggest gaps versus CIS Benchmarks were in account and password settings. The
firewall was the exception: all three profiles were already enabled, matching CIS
recommendations without changes.

Easiest changes: disabling Guest and setting password/lockout policies (built-in tools,
recommended values). Hardest: BitLocker — which in the end could not be completed due to
the mounted ISO drives, a good reminder that hardening depends on the environment, and
that limitations should be documented rather than hidden.
