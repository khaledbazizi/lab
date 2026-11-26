# lab

Automated configuration of servers with Ansible

## Baseline

On each server: an admin user, hardened SSH and a default-deny firewall.

## Services

| Service | What it is | Status |
|---|---|---|
| Web server | nginx | Available |
| VPN | WireGuard | Available |
| Database | PostgreSQL, reachable only through the VPN | Available |
| Backup | restic, with a restore test | Available |

## Requirements

- Ansible
- Supported distros (currently Alpine and Debian)

## Usage

Prepare your servers first: see the [Setup guide](docs/setup.md).

```sh
cd ansible
cp inventory.example.ini inventory.ini    # add your servers and your user
ansible-playbook site.yml --check --diff  # dry run, shows what would change
ansible-playbook site.yml                 # apply
../scripts/verify                         # check the result
```

## Docs

See the [documentation](docs/README.md).

## TODO

- [ ] Try it on a new machine as a user
- [ ] Test Alpine from a fresh install
- [ ] Copy backups to a second machine
- [ ] Monitoring and CI

