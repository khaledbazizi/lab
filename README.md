# Initial setup

## Ansible roles for a secure baseline, using Alpine and Debian

| Role | What it sets up |
|---|---|
| `base` | Deal with packages, install needed tools and set hostname and timezone |
| `users` | Set admin user and add SSH key |
| `ssh_hardening` | No root, no password, limit 3 attempts per connection and disable X11 forwarding |
| `firewall` | nftables for only SSH, ping and DHCP |

## TODO

- [ ] Add documentation for what's already done
- [ ] Add tests
