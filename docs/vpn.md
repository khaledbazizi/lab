# VPN (WireGuard)

## features
private tunnel between device and the server.encrypted traffic through tunnel.

## Enable it
```ini
[vpn]
debian
```
Edit `group_vars/vpn.yml`:
```yaml
wireguard_enabled: true
wireguard_port: 51820
firewall_allowed_udp_ports:
  - "{{ wireguard_port }}"
wireguard_peers:
  - name: laptop
    public_key: "YOUR_DEVICE_PUBLIC_KEY"
    address: 10.10.10.2/32
```
Make your device's keys first. The private key never leaves your device:
```sh
umask 077
wg genkey | tee wg-laptop.key | wg pubkey > wg-laptop.pub
```
Put the contents of `wg-laptop.pub` in `public_key`.

## Settings
| Variable | Default | What it does |
|---|---|---|
| `wireguard_enabled` | `false` | Turns the role on |
| `wireguard_port` | `51820` | UDP port the server listens on |
| `wireguard_address` | `10.10.10.1/24` | The server's address inside the tunnel |
| `wireguard_peers` | empty | Devices allowed to connect |

## Run it
```sh
ansible-playbook site.yml --limit SERVERNAME
```
The server generates its own private key once. It stays on the server and is never in Git.

## Connect from your device
Get the server's public key:
```sh
ansible vpn -b -m command -a "cat /etc/wireguard/server.pub"
```
Create `/etc/wireguard/wg-lab.conf` on your device:
```ini
[Interface]
Address = 10.10.10.2/24
PrivateKey = YOUR_DEVICE_PRIVATE_KEY

[Peer]
PublicKey = SERVER_PUBLIC_KEY
Endpoint = SERVER_IP:51820
AllowedIPs = 10.10.10.1/32
```
Then `sudo wg-quick up wg-lab`.

## Check it works
```sh
ping -c 3 10.10.10.1
sudo wg show
```
You should see replies and a `latest handshake` line.
