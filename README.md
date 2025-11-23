# ansible-hardening-role

![Ansible](https://img.shields.io/badge/Ansible-2.9%2B-blue) ![Security](https://img.shields.io/badge/Security-Hardening-red) ![Linux](https://img.shields.io/badge/Linux-Debian%2FUbuntu-yellow)

## Overview

`ansible-hardening-role` automates immediate, opinionated security hardening for Linux servers (Debian/Ubuntu) using Ansible. The work was guided by an audit performed with Lynis; this role applies quick, high-impact fixes suitable for a fresh VM to raise a baseline Security/Hardening Index.

Version: v1.0 — Goal: apply immediate security fixes to a fresh VM.

Result: the Hardening Index rose from 66 to 76 (+10 points) after a single run.

## What's included (Key features)

- Modular architecture implemented as an Ansible role with focused sub-roles/tasks:
  - SSH hardening (template-based `sshd_config`)
  - Network defenses (UFW firewall + kernel sysctl network protection)
  - System hardening (sysctl tuning, audit tools)
  - Packages (installing and configuring defense tooling)

- SSH Hardening
  - Template-driven `sshd_config` (see `roles/system-hardening/templates/sshd_config.j2`)
  - Disables direct root login
  - Disables password authentication
  - Enforces ED25519 public key usage where possible

- Network Defense
  - UFW (Uncomplicated Firewall) rules to restrict inbound access
  - Kernel sysctl tuning to protect against IP spoofing and related network threats

- Active Defense
  - Installs and configures Fail2Ban with basic jail rules to block brute force attempts

- Compliance and Detection
  - Installs and configures Auditd for auditing events
  - Installs Rkhunter for rootkit checks

## Architecture

Top-level playbook: `site.yml` — applies the `system-hardening` role (see `roles/system-hardening/`).

Key role files (examples):

- `roles/system-hardening/tasks/ssh.yml` — SSH-related tasks
- `roles/system-hardening/tasks/network.yml` — firewall and sysctl network settings
- `roles/system-hardening/tasks/system.yml` — system-wide hardening tasks
- `roles/system-hardening/tasks/packages.yml` — package installation (fail2ban, auditd, rkhunter, etc.)
- `roles/system-hardening/templates/sshd_config.j2` — templated SSH configuration

## Usage

1. Prepare an inventory file (example provided at `inventory/hosts.ini`).
2. Run the playbook with privilege escalation enabled.

Example (from repository root):

```bash
ansible-playbook -i inventory/hosts.ini site.yml --ask-become-pass
```

Notes:
- The playbook requires connectivity to the target host(s) and a user with privilege escalation (sudo).
- Test against a disposable VM before running in production.

## Important security & privacy note

Lynis output files and detailed audit reports may contain sensitive system information. For security and privacy reasons, actual Lynis report `.dat` files and raw audit logs are excluded from this repository. The `reports/` directory in this project intentionally does not contain publishable audit data. If you need to store or share reports, review them and redact or transmit them via secure channels.

## Current status and roadmap

- v1.0 (current): immediate fixes for fresh VMs; +10 hardening index improvement observed after one run.
- v2.0 (planned): partition/isolation recommendations (separate mounts for `/var`, `/tmp`), and more advanced kernel tuning and performance-safe hardening policies.

## Contributing

Contributions are welcome. Please open issues for bugs, feature requests, or proposals for additional hardening checks. When submitting changes, prefer small focused PRs and include rationale and test steps.

## License

This repository does not include a license file by default — add a `LICENSE` if you wish to permit reuse. If you want a recommendation, MIT is a common permissive choice for Ansible roles.

---

If you want, I can also add a quick testing guide (molecule) or example CI job to validate the role on Ubuntu/Debian images.
