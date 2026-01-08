# Linux Hardening Project (v2)

## What's included
- GUIDELINE.md — concise CIS-aligned hardening guideline.
- group_vars/all.yml — variables (users, SSH policy, password policy).
- playbooks/audit.yml — read-only audit; prints PASS/FAIL and writes /tmp/linux_audit_report.html.
- playbooks/enforce.yml — enforces users, SSH, password policy, chrony, unattended-upgrades, AppArmor, auditd, sysctl. **No firewall here** (kept separate).

## Inventory example
Create an `inventory.ini` like:
[linux_hosts]
host1 ansible_host=10.0.0.10
host2 ansible_host=10.0.0.11

## Run
Audit:
  ansible-playbook -i inventory.ini playbooks/audit.yml

Enforce (all baseline tasks):
  ansible-playbook -i inventory.ini playbooks/enforce.yml

Notes:
- Paste PUBLIC keys for `ansibleadmin` and `monadmin` in `group_vars/all.yml`.
- SSH is key-only when `ssh_password_auth: false` (recommended once keys are installed).
- To apply the firewall, use the separate `ansible-firewall-iptables` playbook/ZIP.
