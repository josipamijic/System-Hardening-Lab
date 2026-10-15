# 04 — Lessons Learned & Recommended Baselines

## What this project taught me

- **"Usable" is not "secure".** Across all three systems, a fresh install left important
  settings weak or off by default — Guest accounts enabled, weak password policies, no
  firewall, an exposed Remote Desktop service, missing patches. Hardening is always a
  deliberate, separate step.
- **Method matters more than commands.** Working through *Current state → Risk → Decision
  → Change → Verification → Result* for every item is what makes hardening defensible,
  rather than blindly applying a checklist.
- **Change management protects you.** Allowing SSH before enabling the firewall, backing
  up `sshd_config` before editing it, and verifying access from a second session before
  trusting a change — these habits prevented locking myself out.
- **Document limitations honestly.** BitLocker that couldn't be enabled, updates deferred
  for time, screenshots not retained — recording these is more professional than hiding
  them.
- **Attack surface is about trade-offs.** Promoting a server to a Domain Controller
  *increased* its attack surface on purpose, because the organisation needs that
  function. The goal is to control the necessary surface, not eliminate it.

## Cross-system principles

The three systems differ in tools and syntax but share the same security model:
**identity, privilege, network filtering, patching, and logging** as distinct
mechanisms. The underlying principles are identical — **least privilege, default-deny
networking, defense in depth, and auditability**.

## Next steps

- Apply SSH key-based authentication on the Ubuntu server and disable password auth.
- Complete BitLocker on Windows 11 in an environment without mounted ISO drives.
- Implement privilege tiering and a second Domain Controller for the AD environment.
- **Connect these systems to a SIEM** (see my separate `wazuh-siem-lab` project) so the
  hardening changes and any attacks against them become visible and monitored.
