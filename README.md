# Infrastructure Lab

Automated deployment and configuration 

goal is to build a reusable setup for servers, networking and security

## Ansible roles 

| Role | What it sets up |
|---|---|
| `base` | Deal with packages, install needed tools and set hostname and timezone |
| `users` | Set admin user and add SSH key |
| `ssh_hardening` | No root, no password, limit 3 attempts per connection and disable X11 forwarding |
| `firewall` | nftables for only SSH, ping and DHCP |
| `nginx` | install and configure nginx and open port 80 |

## TODO

### v0.2
- [x] Web server
- [ ] reverse proxy
- [ ] VPN wireguard
- [ ] DNS
- [ ] Support for Enterprise Linux (Rocky)



## Requirements

- Ansible
- SSH access to target servers
- Supported distros (currently Alpine and Debian)

## Usage

See [Setup Guide](setup.md) for configuration.

Run the playbook:

```sh
ansible-playbook -i inventory.ini site.yml
```
