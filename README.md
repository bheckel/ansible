# Ansible Lab – OCI Server Setup

Minimal Ansible project for provisioning and maintaining an Oracle Linux instance (OCI).

Designed to be:

* reproducible
* easy to understand
* easy to tear down and rebuild

---

## Overview

This repo contains:

* **site.yml** – main playbook (base + dotfiles + hardening)
* **fail2ban-setup.yml** – standalone Fail2ban install/config
* **roles/**

  * `base` – core system setup (firewalld, chrony, timezone)
  * `dotfiles` – user environment (vim, tmux, git, symlinks)
  * `hardening` – security-related tasks (in progress)

---

## Requirements

* Control node: Linux / WSL with Python 3
* Target: Oracle Linux 9 (OCI)
* SSH key access to target host

Install Ansible (recommended via venv):

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install ansible
```

---

## Inventory

Copy the template and edit:

```bash
cp inventory.ini.tmplt inventory.ini
```

Example:

```ini
[oci]
YOUR_HOSTNAME ansible_host=YOUR_IP ansible_user=YOUR_USER ansible_ssh_private_key_file=~/.ssh/id_rsa
```

---

## Usage

Run full setup:

```bash
ansible-playbook site.yml
```

Run specific components:

```bash
ansible-playbook site.yml --tags base
ansible-playbook site.yml --tags dotfiles
ansible-playbook site.yml --tags "base,hardening"
```

Run standalone Fail2ban setup:

```bash
ansible-playbook fail2ban-setup.yml
```

---

## Project Structure

```text
.
├── ansible.cfg
├── inventory.ini.tmplt
├── inventory.ini        (local, not committed)
├── site.yml
├── fail2ban-setup.yml
├── roles/
│   ├── base/
│   ├── dotfiles/
│   └── hardening/
└── .venv/   (ignored)
```

---

## Notes

* `.venv/` is not committed (reproducible via pip)
* `inventory.ini` is not committed (contains host-specific info)
* Dotfiles are symlinked from `~/dotfiles` repo on the target host
* Tasks are idempotent where possible

---

## Safety

* No secrets are stored in this repo
* SSH keys are referenced, not included
* Add your IP to Fail2ban `ignoreip` to avoid lockout

---

## Troubleshooting

Check connectivity:

```bash
ansible oci -m ping
```

Verbose output:

```bash
ansible-playbook site.yml -vvv
```

---

## Future Work

* Expand hardening role (SSH config, audit rules)
* Template-based configs instead of inline content
* Multi-host inventory support

---

## Philosophy

Keep it simple:

* small roles
* readable tasks
* minimal magic

If it can’t be understood in a few minutes, it’s too complicated.

