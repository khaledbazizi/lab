# Database (PostgreSQL)

## features
PostgreSQL database, access via VPN address, port 5432 open by firewall on tunnel.

## Enable it
The host must also be in `vpn`, because PostgreSQL binds to the VPN address:
```ini
[vpn]
debian

[databases]
debian
```
`group_vars/databases.yml`:
```yaml
postgresql_enabled: true
postgresql_listen_addresses: "localhost,10.10.10.1"
postgresql_wait_for_wireguard: true
firewall_vpn_tcp_ports:
  - 5432
```

## Settings
| Variable | Default | What it does |
|---|---|---|
| `postgresql_enabled` | `false` | Turns the role on |
| `postgresql_listen_addresses` | `localhost` | Addresses it listens on |
| `postgresql_db` | `lab` | Database to create |
| `postgresql_user` | `lab` | Database user to create |
| `postgresql_allowed_network` | `10.10.10.0/24` | Network allowed to log in |
| `postgresql_wait_for_wireguard` | `false` | Start after the VPN, so the address exists |
| `firewall_vpn_tcp_ports` | empty | TCP ports opened only on the tunnel interface |

## Run it
```sh
ansible-playbook site.yml --limit SERVERNAME
```
The password is generated on the server in `/root/.postgresql-lab-password`. It is never in Git.

## Check it works
From outside the tunnel, the port must be closed:
```sh
nc -zv -w 3 SERVER_IP 5432
```
This should time out. With the tunnel up:
```sh
nc -zv -w 3 10.10.10.1 5432
```
This should succeed. `scripts/verify` checks both.

