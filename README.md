# Runbook: Firewall + Hardening

## 0) Prepare
```bash
python3 -m pip install --user ansible ansible-lint
ansible-galaxy collection install -r requirements.yml
```

## 1) Add your keys
- Place any **OpenSSH** public keys for human access under `files/ssh/` matching `name*.pub`
  (You can create one from a PEM private key with: `ssh-keygen -y -f name.pem > files/ssh/name-from-pem.pub`)
- OR paste key strings into `group_vars/all.yml` under `name_keys_human`.
- Optional automation key: `name_automation_pubkey` and restrict with `ansible_controller_cidrs`.

## 2) Set networks
Edit `group_vars/all.yml`:
```yaml
admin_cidrs: ["IP","IP"]
world_ports: []       # keep empty unless needed
admin_ports: ["22/tcp"]
```

## 3) Safe order (avoid lockouts)
```bash
# A) Push keys and SSH config (password auth still ON for safety)
ansible-playbook -i inventories/prod/inventory.ini playbooks/users.yml

# Test a new SSH session using your key BEFORE flipping passwords off.
ssh -i ~/.ssh/yourkey user@IP

# B) Enforce key-only auth
#    Set name_disable_password_auth: true in group_vars/all.yml, then:
ansible-playbook -i inventories/prod/inventory.ini playbooks/users.yml

# C) Hardening (your audit -> enforce)
ansible-playbook -i inventories/prod/inventory.ini playbooks/hardening/audit.yml --check
ansible-playbook -i inventories/prod/inventory.ini playbooks/hardening/audit.yml
ansible-playbook -i inventories/prod/inventory.ini playbooks/hardening/enforce.yml --check
ansible-playbook -i inventories/prod/inventory.ini playbooks/hardening/enforce.yml

# D) Firewall (verify CIDRs are correct!)
ansible-playbook -i inventories/prod/inventory.ini playbooks/firewall.yml --check
ansible-playbook -i inventories/prod/inventory.ini playbooks/firewall.yml

# E) Or run all phases sequentially (one host at a time)
ansible-playbook -i inventories/prod/inventory.ini playbooks/site.yml
```
