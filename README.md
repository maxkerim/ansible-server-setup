# Ansible server setup

An Ansible playbook for preparing a Debian/Ubuntu web server.

## Roles

The playbook runs these roles in order:

- `base`: updates packages, installs common utilities and enables `fail2ban`.
- `nginx`: installs nginx, deploys its site configuration and enables the service.
- `app`: uploads `roles/app/files/website.tar.gz` and extracts the website into `/var/www/html`.
- `ssh`: adds the committed public key from `roles/ssh/files/id_ed25519.pub` to root's `authorized_keys`.

Each role can be run separately with its matching tag: `base`, `nginx`, `app` or `ssh`.

## Requirements

- Ansible on the control machine.
- The `ansible.posix` collection: `ansible-galaxy collection install ansible.posix`.
- A reachable Debian/Ubuntu host, SSH access, and a connection user allowed to become root.
- The website archive at `roles/app/files/website.tar.gz` (already included in this repository).

## Configure inventory

Copy the example and set the target host and connection user in the local, ignored file:

```sh
cp inventory.example.ini inventory.ini
```

The example uses the documentation-only address `192.0.2.10`. Replace it with your server's address. Do not commit real host addresses, usernames tied to production infrastructure, passwords, private keys, or other credentials. Keep secrets in a local untracked file or use Ansible Vault.

## Run

Check the playbook syntax:

```sh
ansible-playbook -i inventory.ini setup.yml --syntax-check
```

Apply all roles:

```sh
ansible-playbook -i inventory.ini setup.yml
```

Run one role, for example nginx:

```sh
ansible-playbook -i inventory.ini setup.yml --tags nginx
```

The play uses privilege escalation (`become: true`), so the inventory connection user must have permission to become root. The SSH role installs the repository's public key for root; it does not provision the initial connection user.

## Lint

Install `ansible-lint` and run it from the repository root:

```sh
python -m pip install ansible-lint
ansible-lint
```

GitHub Actions runs this lint check for pushes and pull requests.
