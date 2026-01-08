# Linux Hardening Guideline (Grafana/Prometheus nodes)

**Accounts & SSH**
- Two sudo-capable accounts: `ansibleadmin`, `monadmin`.
- SSH key-only auth after bootstrap; disable root SSH.
- Restrict SSH to authorized users (AllowUsers).

**Auth & Password Policy**
- PAM pwquality: min length ≥ 14; enforce character classes.
- faillock: lock out after 5 failed attempts for 10 minutes.

**Logging, Time, and Auditing**
- Enable `auditd`, rotate logs, and forward to SIEM if available.
- Ensure time sync via `chrony` (or systemd-timesyncd where applicable).

**System Security**
- AppArmor enabled.
- Kernel/sysctl network hardening: disable redirects, source routing; enable SYN cookies.

**Patching**
- Install `unattended-upgrades` for automatic security updates.

**Firewall**
- Keep host firewall in a separate playbook (iptables) allowing SSH only from approved subnets and leaving monitoring ports open.
